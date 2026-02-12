# vertaal-machine
een vertaal machine voor mensen
"""
Offline Nederlandse naar Spaanse vertaalmachine met GUI
Vereist: pip install transformers torch sentencepiece tkinter
"""

import tkinter as tk
from tkinter import scrolledtext, messagebox, ttk
import threading
from transformers import pipeline
import sys

class TranslatorGUI:
    """GUI voor offline vertaling Nederlands naar Spaans"""
    
    def __init__(self, root):
        """Initialiseer de GUI"""
        self.root = root
        self.root.title("Nederlands ↔ Spaans Vertaalmachine")
        self.root.geometry("1000x600")
        self.root.configure(bg="#f0f0f0")
        
        self.translator = None
        self.reverse_translator = None
        self.is_translating = False
        
        # Maak UI
        self.create_ui()
        
        # Laad model in achtergrond
        threading.Thread(target=self.load_model, daemon=True).start()
    
    def create_ui(self):
        """Maak de gebruikersinterface"""
        
        # Titel
        title_frame = tk.Frame(self.root, bg="#2c3e50")
        title_frame.pack(fill=tk.X, padx=0, pady=0)
        
        title_label = tk.Label(
            title_frame,
            text="🌐 Vertaalmachine: Nederlands ↔ Spaans",
            font=("Arial", 18, "bold"),
            bg="#2c3e50",
            fg="white",
            pady=10
        )
        title_label.pack()
        
        # Hoofdframe met twee kolommen
        main_frame = tk.Frame(self.root, bg="#f0f0f0")
        main_frame.pack(fill=tk.BOTH, expand=True, padx=10, pady=10)
        
        # NEDERLANDSE KOLOM (LINKS)
        left_frame = tk.Frame(main_frame, bg="white", relief=tk.RIDGE, bd=2)
        left_frame.pack(side=tk.LEFT, fill=tk.BOTH, expand=True, padx=(0, 5))
        
        nl_header = tk.Label(
            left_frame,
            text="🇳🇱 NEDERLANDS",
            font=("Arial", 14, "bold"),
            bg="#3498db",
            fg="white",
            pady=8
        )
        nl_header.pack(fill=tk.X)
        
        self.dutch_text = scrolledtext.ScrolledText(
            left_frame,
            height=15,
            width=40,
            font=("Arial", 11),
            wrap=tk.WORD,
            bg="#ecf0f1",
            fg="#2c3e50"
        )
        self.dutch_text.pack(fill=tk.BOTH, expand=True, padx=8, pady=8)
        self.dutch_text.bind("<KeyRelease>", lambda e: self.on_text_change())
        
        # Karakterteller links
        self.nl_char_label = tk.Label(
            left_frame,
            text="Karakters: 0",
            font=("Arial", 9),
            bg="white",
            fg="#7f8c8d"
        )
        self.nl_char_label.pack(anchor="e", padx=8, pady=4)
        
        # Knopen links
        left_button_frame = tk.Frame(left_frame, bg="white")
        left_button_frame.pack(fill=tk.X, padx=8, pady=8)
        
        clear_nl_btn = tk.Button(
            left_button_frame,
            text="Wissen",
            command=self.clear_dutch,
            bg="#e74c3c",
            fg="white",
            font=("Arial", 10, "bold"),
            cursor="hand2"
        )
        clear_nl_btn.pack(side=tk.LEFT, padx=4)
        
        copy_nl_btn = tk.Button(
            left_button_frame,
            text="Kopiëren",
            command=self.copy_dutch,
            bg="#27ae60",
            fg="white",
            font=("Arial", 10, "bold"),
            cursor="hand2"
        )
        copy_nl_btn.pack(side=tk.LEFT, padx=4)
        
        # SPAANSE KOLOM (RECHTS)
        right_frame = tk.Frame(main_frame, bg="white", relief=tk.RIDGE, bd=2)
        right_frame.pack(side=tk.RIGHT, fill=tk.BOTH, expand=True, padx=(5, 0))
        
        es_header = tk.Label(
            right_frame,
            text="🇪🇸 SPAANS",
            font=("Arial", 14, "bold"),
            bg="#e74c3c",
            fg="white",
            pady=8
        )
        es_header.pack(fill=tk.X)
        
        self.spanish_text = scrolledtext.ScrolledText(
            right_frame,
            height=15,
            width=40,
            font=("Arial", 11),
            wrap=tk.WORD,
            bg="#ecf0f1",
            fg="#2c3e50",
            state=tk.DISABLED
        )
        self.spanish_text.pack(fill=tk.BOTH, expand=True, padx=8, pady=8)
        
        # Karakterteller rechts
        self.es_char_label = tk.Label(
            right_frame,
            text="Karakters: 0",
            font=("Arial", 9),
            bg="white",
            fg="#7f8c8d"
        )
        self.es_char_label.pack(anchor="e", padx=8, pady=4)
        
        # Knopen rechts
        right_button_frame = tk.Frame(right_frame, bg="white")
        right_button_frame.pack(fill=tk.X, padx=8, pady=8)
        
        clear_es_btn = tk.Button(
            right_button_frame,
            text="Wissen",
            command=self.clear_spanish,
            bg="#e74c3c",
            fg="white",
            font=("Arial", 10, "bold"),
            cursor="hand2"
        )
        clear_es_btn.pack(side=tk.LEFT, padx=4)
        
        copy_es_btn = tk.Button(
            right_button_frame,
            text="Kopiëren",
            command=self.copy_spanish,
            bg="#27ae60",
            fg="white",
            font=("Arial", 10, "bold"),
            cursor="hand2"
        )
        copy_es_btn.pack(side=tk.LEFT, padx=4)
        
        # ONDERSTE FRAME MET SWAPKNOP
        bottom_frame = tk.Frame(self.root, bg="#f0f0f0")
        bottom_frame.pack(fill=tk.X, padx=10, pady=10)
        
        swap_btn = tk.Button(
            bottom_frame,
            text="⇅ VERWISSELEN",
            command=self.swap_languages,
            bg="#9b59b6",
            fg="white",
            font=("Arial", 11, "bold"),
            cursor="hand2",
            width=20
        )
        swap_btn.pack(side=tk.LEFT, padx=5)
        
        self.status_label = tk.Label(
            bottom_frame,
            text="Model laden...",
            font=("Arial", 10),
            bg="#f0f0f0",
            fg="#e74c3c"
        )
        self.status_label.pack(side=tk.LEFT, padx=20)
    
    def load_model(self):
        """Laad het vertaalmodel"""
        try:
            self.status_label.config(text="🔄 Nederlands→Spaans model laden...", fg="#f39c12")
            self.root.update()
            
            self.translator = pipeline(
                "translation_nl_to_es",
                model="Helsinki-NLP/Tatoeba-MT-models_nl-es"
            )
            
            self.status_label.config(text="🔄 Spaans→Nederlands model laden...", fg="#f39c12")
            self.root.update()
            
            self.reverse_translator = pipeline(
                "translation_es_to_nl",
                model="Helsinki-NLP/Tatoeba-MT-models_es-nl"
            )
            
            self.status_label.config(
                text="✓ Klaar! Begin met typen in het Nederlands veld",
                fg="#27ae60"
            )
        except Exception as e:
            self.status_label.config(
                text=f"✗ Fout bij laden: {str(e)}",
                fg="#e74c3c"
            )
            messagebox.showerror(
                "Fout",
                f"Model kon niet geladen worden:\n{e}\n\n"
                "Installeer: pip install transformers torch sentencepiece"
            )
    
    def on_text_change(self):
        """Reactie op tekstwijziging"""
        if self.is_translating:
            return
        
        dutch_text = self.dutch_text.get("1.0", tk.END).strip()
        
        # Update karakterteller
        self.nl_char_label.config(text=f"Karakters: {len(dutch_text)}")
        
        if not dutch_text:
            self.spanish_text.config(state=tk.NORMAL)
            self.spanish_text.delete("1.0", tk.END)
            self.spanish_text.config(state=tk.DISABLED)
            self.es_char_label.config(text="Karakters: 0")
            return
        
        if self.translator is None:
            return
        
        # Vertaal in achtergrond
        threading.Thread(
            target=self.translate_text,
            args=(dutch_text,),
            daemon=True
        ).start()
    
    def translate_text(self, text):
        """Vertaal Nederlands naar Spaans"""
        try:
            self.is_translating = True
            
            if len(text) > 500:
                sentences = text.split('.')
                translations = []
                for sentence in sentences:
                    if sentence.strip():
                        result = self.translator(sentence.strip(), max_length=512)
                        translations.append(result[0]['translation_text'])
                translated = '. '.join(translations)
            else:
                result = self.translator(text, max_length=512)
                translated = result[0]['translation_text']
            
            # Update GUI
            self.spanish_text.config(state=tk.NORMAL)
            self.spanish_text.delete("1.0", tk.END)
            self.spanish_text.insert("1.0", translated)
            self.spanish_text.config(state=tk.DISABLED)
            self.es_char_label.config(text=f"Karakters: {len(translated)}")
            
        except Exception as e:
            self.spanish_text.config(state=tk.NORMAL)
            self.spanish_text.delete("1.0", tk.END)
            self.spanish_text.insert("1.0", f"Fout: {e}")
            self.spanish_text.config(state=tk.DISABLED)
        finally:
            self.is_translating = False
    
    def clear_dutch(self):
        """Wis Nederlandse tekst"""
        self.dutch_text.delete("1.0", tk.END)
        self.spanish_text.config(state=tk.NORMAL)
        self.spanish_text.delete("1.0", tk.END)
        self.spanish_text.config(state=tk.DISABLED)
        self.nl_char_label.config(text="Karakters: 0")
        self.es_char_label.config(text="Karakters: 0")
    
    def clear_spanish(self):
        """Wis Spaanse tekst"""
        self.spanish_text.config(state=tk.NORMAL)
        self.spanish_text.delete("1.0", tk.END)
        self.spanish_text.config(state=tk.DISABLED)
        self.es_char_label.config(text="Karakters: 0")
    
    def copy_dutch(self):
        """Kopieer Nederlandse tekst"""
        text = self.dutch_text.get("1.0", tk.END).strip()
        if text:
            self.root.clipboard_clear()
            self.root.clipboard_append(text)
            messagebox.showinfo("Gekopieerd", "Nederlandse tekst gekopieerd naar klembord!")
    
    def copy_spanish(self):
        """Kopieer Spaanse tekst"""
        text = self.spanish_text.get("1.0", tk.END).strip()
        if text:
            self.root.clipboard_clear()
            self.root.clipboard_append(text)
            messagebox.showinfo("Gekopieerd", "Spaanse tekst gekopieerd naar klembord!")
    
    def swap_languages(self):
        """Verwisselen Nederlands ↔ Spaans"""
        dutch = self.dutch_text.get("1.0", tk.END).strip()
        spanish = self.spanish_text.get("1.0", tk.END).strip()
        
        self.dutch_text.delete("1.0", tk.END)
        self.dutch_text.insert("1.0", spanish)
        
        self.spanish_text.config(state=tk.NORMAL)
        self.spanish_text.delete("1.0", tk.END)
        self.spanish_text.insert("1.0", dutch)
        self.spanish_text.config(state=tk.DISABLED)
        
        self.on_text_change()


def main():
    """Hoofdfunctie"""
    root = tk.Tk()
    app = TranslatorGUI(root)
    root.mainloop()


if __name__ == "__main__":
    main()
