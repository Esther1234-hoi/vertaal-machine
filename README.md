# vertaal-machine
een vertaal machine voor mensen
"""
Offline Nederlandse naar Spaanse vertaalmachine
Vereist: pip install transformers torch sentencepiece
"""

from transformers import pipeline
import sys

class DutchToSpanishTranslator:
    """Vertaalmachine van Nederlands naar Spaans"""
    
    def __init__(self):
        """Initialiseer het vertaalmodel"""
        print("Vertaalmodel laden... Dit kan de eerste keer even duren.")
        try:
            # Laad het Helsinki-NLP model voor NL->ES vertaling
            self.translator = pipeline(
                "translation_nl_to_es",
                model="Helsinki-NLP/Tatoeba-MT-models_nl-es"
            )
            print("✓ Vertaalmodel succesvol geladen!")
        except Exception as e:
            print(f"Fout bij laden model: {e}")
            print("Zorg ervoor dat je de vereiste packages hebt geïnstalleerd:")
            print("pip install transformers torch sentencepiece")
            sys.exit(1)
    
    def translate(self, text):
        """Vertaal Nederlandse tekst naar Spaans"""
        if not text.strip():
            return ""
        
        try:
            result = self.translator(text, max_length=512)
            return result[0]['translation_text']
        except Exception as e:
            return f"Fout bij vertaling: {e}"
    
    def translate_paragraph(self, text):
        """Vertaal een lange tekst zinnen per zin"""
        sentences = text.split('.')
        translated_sentences = []
        
        for sentence in sentences:
            sentence = sentence.strip()
            if sentence:
                translated = self.translate(sentence + '.')
                translated_sentences.append(translated)
        
        return ' '.join(translated_sentences)
    
    def interactive_mode(self):
        """Interactieve modus voor continue vertaling"""
        print("\n" + "="*50)
        print("NEDERLANDSE → SPAANSE VERTAALMACHINE")
        print("="*50)
        print("Typ 'exit' of 'quit' om af te sluiten\n")
        
        while True:
            try:
                dutch_text = input("Nederlands: ").strip()
                
                if dutch_text.lower() in ['exit', 'quit', 'q']:
                    print("Tot ziens!")
                    break
                
                if not dutch_text:
                    print("Voer alstublieft wat tekst in.\n")
                    continue
                
                spanish_text = self.translate(dutch_text)
                print(f"Spaans:     {spanish_text}\n")
                
            except KeyboardInterrupt:
                print("\n\nVertaling onderbroken.")
                break
            except Exception as e:
                print(f"Fout: {e}\n")
    
    def batch_translate(self, texts):
        """Vertaal meerdere zinnen"""
        results = []
        for text in texts:
            translated = self.translate(text)
            results.append({
                'original': text,
                'translated': translated
            })
        return results
    
    def save_translation(self, filename, original, translated):
        """Sla vertaling op in een bestand"""
        try:
            with open(filename, 'a', encoding='utf-8') as f:
                f.write(f"NL: {original}\n")
                f.write(f"ES: {translated}\n")
                f.write("-" * 40 + "\n")
            print(f"✓ Vertaling opgeslagen in {filename}")
        except Exception as e:
            print(f"Fout bij opslaan: {e}")


def main():
    """Hoofdfunctie"""
    translator = DutchToSpanishTranslator()
    
    # Voorbeeldteksten
    examples = [
        "Hallo, hoe gaat het met je?",
        "Dit is een offline vertaalmachine.",
        "Het weer is mooi vandaag.",
        "Ik hou van programmeren in Python.",
        "Welkom bij de vertaalmachine!"
    ]
    
    print("\n" + "="*50)
    print("VOORBEELDEN VAN VERTALINGEN")
    print("="*50 + "\n")
    
    for dutch in examples:
        spanish = translator.translate(dutch)
        print(f"NL: {dutch}")
        print(f"ES: {spanish}\n")
    
    # Start interactieve modus
    translator.interactive_mode()


if __name__ == "__main__":
    main()
