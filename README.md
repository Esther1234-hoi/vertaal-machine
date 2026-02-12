# vertaal-machine
een vertaal machine voor mensen
"""
Offline Nederlandse naar Spaanse vertaalmachine met GUI
Woordenboek: 200 woorden (werkwoorden, huis, dieren, eten, drinken, voorstel)
Vereist: pip install tkinter
"""

import tkinter as tk
from tkinter import scrolledtext, messagebox, ttk
import json

class TranslatorGUI:
    """GUI voor offline vertaling Nederlands naar Spaans met woordenboek"""
    
    def __init__(self, root):
        """Initialiseer de GUI"""
        self.root = root
        self.root.title("Nederlands ↔ Spaans Vertaalmachine - 200 Woorden")
        self.root.geometry("1100x700")
        self.root.configure(bg="#f0f0f0")
        
        # Woordenboek laden
        self.woordenboek = self.create_woordenboek()
        
        # Maak UI
        self.create_ui()
    
    def create_woordenboek(self):
        """Maak een woordenboek met 200 woorden"""
        return {
            # WERKWOORDEN (50 woorden)
            "werkwoorden": {
                # Basiswerkwoorden
                "zijn": "ser",
                "hebben": "tener",
                "doen": "hacer",
                "gaan": "ir",
                "komen": "venir",
                "zien": "ver",
                "geven": "dar",
                "nemen": "tomar",
                "maken": "hacer",
                "zeggen": "decir",
                "weten": "saber",
                "kunnen": "poder",
                "willen": "querer",
                "moeten": "deber",
                "mogen": "poder",
                "denken": "pensar",
                "voelen": "sentir",
                "houden": "sostener",
                "brengen": "traer",
                "stellen": "poner",
                
                # Dagelijkse activiteiten
                "slapen": "dormir",
                "eten": "comer",
                "drinken": "beber",
                "werken": "trabajar",
                "spelen": "jugar",
                "leren": "aprender",
                "lezen": "leer",
                "schrijven": "escribir",
                "spreken": "hablar",
                "luisteren": "escuchar",
                "kijken": "mirar",
                "lopen": "caminar",
                "rennen": "correr",
                "zitten": "sentarse",
                "staan": "estar de pie",
                "liggen": "acostarse",
                "vallen": "caer",
                "rijden": "conducir",
                "vliegen": "volar",
                "zwemmen": "nadar",
                
                # Meer werkwoorden
                "wassen": "lavar",
                "schoonmaken": "limpiar",
                "koken": "cocinar",
                "bakken": "hornear",
                "snijden": "cortar",
                "mengen": "mezclar",
                "proeven": "probar",
                "helpen": "ayudar",
                "begrijpen": "entender",
                "vergeten": "olvidar"
            },
            
            # HUIS (40 woorden)
            "huis": {
                "huis": "casa",
                "appartement": "apartamento",
                "kamer": "habitación",
                "slaapkamer": "dormitorio",
                "badkamer": "baño",
                "keuken": "cocina",
                "woonkamer": "sala de estar",
                "eetkamer": "comedor",
                "deur": "puerta",
                "raam": "ventana",
                "muur": "pared",
                "vloer": "suelo",
                "plafond": "techo",
                "trap": "escaleras",
                "balkon": "balcón",
                "tuin": "jardín",
                "garage": "garaje",
                
                # Meubilair
                "bed": "cama",
                "tafel": "mesa",
                "stoel": "silla",
                "bank": "sofá",
                "kast": "armario",
                "boekenkast": "estantería",
                "bureau": "escritorio",
                "lamp": "lámpara",
                "spiegel": "espejo",
                "gordijn": "cortina",
                "kleed": "alfombra",
                
                # Huishoudelijk
                "koelkast": "nevera",
                "oven": "horno",
                "fornuis": "estufa",
                "gootsteen": "fregadero",
                "vaatwasser": "lavavajillas",
                "wasmachine": "lavadora",
                "televisie": "televisor",
                "computer": "ordenador",
                "telefoon": "teléfono",
                "radio": "radio",
                "klok": "reloj"
            },
            
            # DIEREN (35 woorden)
            "dieren": {
                "hond": "perro",
                "kat": "gato",
                "paard": "caballo",
                "koe": "vaca",
                "schaap": "oveja",
                "geit": "cabra",
                "varken": "cerdo",
                "kip": "pollo",
                "eend": "pato",
                "gans": "ganso",
                "duif": "paloma",
                "vogel": "pájaro",
                "papegaai": "loro",
                "uil": "búho",
                "adelaar": "águila",
                "vis": "pez",
                "zalm": "salmón",
                "schildpad": "tortuga",
                "slang": "serpiente",
                "kikker": "rana",
                "bees": "abeja",
                "vlieg": "mosca",
                "mug": "mosquito",
                "rups": "oruga",
                "spin": "araña",
                "konijn": "conejo",
                "muis": "ratón",
                "rat": "rata",
                "vos": "zorro",
                "beer": "oso",
                "leeuw": "león",
                "tijger": "tigre",
                "aap": "mono",
                "olifant": "elefante",
                "giraffe": "jirafa"
            },
            
            # ETEN (35 woorden)
            "eten": {
                "brood": "pan",
                "boter": "mantequilla",
                "kaas": "queso",
                "ham": "jamón",
                "vlees": "carne",
                "kip": "pollo",
                "vis": "pescado",
                "eieren": "huevos",
                "melk": "leche",
                "yoghurt": "yogur",
                "appel": "manzana",
                "pear": "pera",
                "banaan": "plátano",
                "sinaasappel": "naranja",
                "citroen": "limón",
                "aardbei": "fresa",
                "kers": "cereza",
                "druif": "uva",
                "watermeloen": "sandía",
                "meloen": "melón",
                "sla": "lechuga",
                "tomaat": "tomate",
                "komkommer": "pepino",
                "wortel": "zanahoria",
                "aardappel": "patata",
                "ui": "cebolla",
                "knoflook": "ajo",
                "rijst": "arroz",
                "pasta": "pasta",
                "soep": "sopa",
                "pizza": "pizza",
                "sandwich": "sándwich",
                "salade": "ensalada",
                "dessert": "postre",
                "chocolade": "chocolate"
            },
            
            # DRINKEN (15 woorden)
            "drinken": {
                "water": "agua",
                "melk": "leche",
                "koffie": "café",
                "thee": "té",
                "sap": "zumo",
                "sinaasappelsap": "zumo de naranja",
                "appelsap": "zumo de manzana",
                "limonade": "limonada",
                "frisdrank": "refresco",
                "bier": "cerveza",
                "wijn": "vino",
                "rode wijn": "vino tinto",
                "witte wijn": "vino blanco",
                "champagne": "champán",
                "whisky": "whisky"
            },
            
            # VOORSTEL/INTRODUCTIE (25 woorden)
            "voorstel": {
                "hallo": "hola",
                "goedemorgen": "buenos días",
                "goedemiddag": "buenas tardes",
                "goedenavond": "buenas noches",
                "welkom": "bienvenido",
                "aangenaam": "encantado",
                "mijn naam is": "mi nombre es",
                "ik heet": "me llamo",
                "wat is uw naam": "¿cuál es su nombre?",
                "hoe heet je": "¿cómo te llamas?",
                "ik ben": "yo soy",
                "ik kom uit": "vengo de",
                "ik ben Nederlands": "soy holandés",
                "ik ben van Nederland": "soy de Holanda",
                "het is een genoegen": "es un placer",
                "dank u wel": "gracias",
                "tot ziens": "adiós",
                "tot straks": "hasta luego",
                "fijne dag": "que tengas un buen día",
                "veel sterkte": "mucho ánimo",
                "veel plezier": "que disfrutes",
                "hoe gaat het": "¿cómo estás?",
                "het gaat goed": "estoy bien",
                "ik ben blij": "estoy feliz",
                "prettig kennis te maken": "es un placer conocerte"
            }
        }
    
    def create_ui(self):
        """Maak de gebruikersinterface"""
        
        # Titel
        title_frame = tk.Frame(self.root, bg="#2c3e50")
        title_frame.pack(fill=tk.X, padx=0, pady=0)
        
        title_label = tk.Label(
            title_frame,
            text="🌐 Vertaalmachine: Nederlands ↔ Spaans (200 Woorden)",
            font=("Arial", 16, "bold"),
            bg="#2c3e50",
            fg="white",
            pady=10
        )
        title_label.pack()
        
        subtitle_label = tk.Label(
            title_frame,
            text="Categorieën: Werkwoorden • Huis • Dieren • Eten • Drinken • Voorstel",
            font=("Arial", 9),
            bg="#2c3e50",
            fg="#ecf0f1",
            pady=5
        )
        subtitle_label.pack()
        
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
        
        # ONDERSTE FRAME
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
        
        woorden_btn = tk.Button(
            bottom_frame,
            text="📚 WOORDENBOEK",
            command=self.show_woordenboek,
            bg="#16a085",
            fg="white",
            font=("Arial", 11, "bold"),
            cursor="hand2"
        )
        woorden_btn.pack(side=tk.LEFT, padx=5)
        
        self.status_label = tk.Label(
            bottom_frame,
            text="✓ Klaar! Begin met typen in het Nederlands veld",
            font=("Arial", 10),
            bg="#f0f0f0",
            fg="#27ae60"
        )
        self.status_label.pack(side=tk.LEFT, padx=20)
    
    def translate_word(self, word):
        """Vertaal een woord met het woordenboek"""
        word_lower = word.lower().strip()
        
        for category, words in self.woordenboek.items():
            if word_lower in words:
                return words[word_lower]
        
        return None
    
    def on_text_change(self):
        """Reactie op tekstwijziging"""
        dutch_text = self.dutch_text.get("1.0", tk.END).strip()
        
        # Update karakterteller
        self.nl_char_label.config(text=f"Karakters: {len(dutch_text)}")
        
        if not dutch_text:
            self.spanish_text.config(state=tk.NORMAL)
            self.spanish_text.delete("1.0", tk.END)
            self.spanish_text.config(state=tk.DISABLED)
            self.es_char_label.config(text="Karakters: 0")
            return
        
        # Vertaal woord voor woord
        words = dutch_text.split()
        translated_words = []
        
        for word in words:
            # Verwijder leestekens
            clean_word = word.rstrip('.,!?;:')
            punctuation = word[len(clean_word):]
            
            translation = self.translate_word(clean_word)
            if translation:
                translated_words.append(translation + punctuation)
            else:
                translated_words.append(word)
        
        translated_text = ' '.join(translated_words)
        
        # Update GUI
        self.spanish_text.config(state=tk.NORMAL)
        self.spanish_text.delete("1.0", tk.END)
        self.spanish_text.insert("1.0", translated_text)
        self.spanish_text.config(state=tk.DISABLED)
        self.es_char_label.config(text=f"Karakters: {len(translated_text)}")
    
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
            messagebox.showinfo("Gekopieerd", "Nederlandse tekst gekopieerd!")
    
    def copy_spanish(self):
        """Kopieer Spaanse tekst"""
        text = self.spanish_text.get("1.0", tk.END).strip()
        if text:
            self.root.clipboard_clear()
            self.root.clipboard_append(text)
            messagebox.showinfo("Gekopieerd", "Spaanse tekst gekopieerd!")
    
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
    
    def show_woordenboek(self):
        """Toon het volledige woordenboek"""
        window = tk.Toplevel(self.root)
        window.title("Woordenboek - 200 Woorden")
        window.geometry("800x600")
        window.configure(bg="#f0f0f0")
        
        # Notebook (tabs)
        notebook = ttk.Notebook(window)
        notebook.pack(fill=tk.BOTH, expand=True, padx=10, pady=10)
        
        colors = {
            "werkwoorden": "#3498db",
            "huis": "#f39c12",
            "dieren": "#e74c3c",
            "eten": "#27ae60",
            "drinken": "#9b59b6",
            "voorstel": "#16a085"
        }
        
        for category, words in self.woordenboek.items():
            frame = ttk.Frame(notebook)
            notebook.add(frame, text=f"{category.capitalize()} ({len(words)})")
            
            text_widget = scrolledtext.ScrolledText(
                frame,
                height=20,
                width=80,
                font=("Arial", 10),
                wrap=tk.WORD,
                bg="#ecf0f1"
            )
            text_widget.pack(fill=tk.BOTH, expand=True, padx=5, pady=5)
            
            content = ""
            for nl, es in sorted(words.items()):
                content += f"{nl.ljust(20)} → {es}\n"
            
            text_widget.insert("1.0", content)
            text_widget.config(state=tk.DISABLED)


def main():
    """Hoofdfunctie"""
    root = tk.Tk()
    app = TranslatorGUI(root)
    root.mainloop()


if __name__ == "__main__":
    main()
