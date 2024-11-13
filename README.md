# my-google-translate-widget
Prosty widget do tłumaczenia strony na inne języki z użyciem Google Translate. Łatwy sposób na dodanie tłumaczenia bez obciążających wtyczek.

# Widget Automatycznego Tłumaczenia Strony

Prosty widget tłumaczenia strony, który umożliwia automatyczne tłumaczenie zawartości z języka polskiego na angielski (lub inne języki). Dzięki temu rozwiązaniu można uniknąć obciążających wtyczek tłumaczeniowych, wykorzystując Google Translate w sposób lekki i łatwy do implementacji. Widget jest lekki, szybki i daje użytkownikowi możliwość zmiany języka na stronie.

## Funkcjonalności:

- **Przełączanie między językami**: Widget umożliwia użytkownikom wybór języka tłumaczenia z listy dostępnych języków (np. polski i angielski).
- **Brak obciążających wtyczek**: Kod działa w prosty sposób, wykorzystując Google Translate, bez potrzeby instalowania dodatkowych, ciężkich wtyczek.
- **Estetyczny interfejs**: Widget ma minimalistyczny wygląd, który można dostosować za pomocą CSS. Początkowo pojawia się w górnaj części strony.
- **Responsywność**: Widget jest przyjazny dla urządzeń mobilnych, ale domyślnie pojawia się w górnej części ekranu. Można dostosować jego pozycję w kodzie CSS, aby zmienić miejsce, w którym się pojawia (np. prawy dolny róg).

## Jak zainstalować i używać

Aby dodać widget tłumaczenia do swojej strony, wykonaj poniższe kroki:

1. **Przygotowanie motywu potomnego (child theme)**:
   - Upewnij się, że używasz motywu potomnego w WordPressie. Dzięki temu zmiany w `functions.php` nie zostaną nadpisane podczas aktualizacji motywu głównego.

2. **Dodanie kodu do `functions.php`**:
   - Skopiuj poniższy kod i wklej go do pliku `functions.php` w motywie potomnym.

3. **Dostosowanie języków**:
   - Domyślnie widget tłumaczy stronę z języka polskiego (`pl`) na angielski (`en`). Możesz zmienić te ustawienia, edytując parametr `includedLanguages` w funkcji JavaScript w kodzie.

4. **Stylizacja widgetu**:
   - Kod zawiera domyślną stylizację widgetu, ale możesz dowolnie edytować styl CSS, aby pasował do wyglądu Twojej strony. Dostosuj kolory, pozycję przycisku lub rozmiar widgetu według potrzeb.

5. **Testowanie**:
   - Po dodaniu kodu i wprowadzeniu zmian na stronie, przetestuj widget tłumaczenia, klikając na ikonę globu w prawym dolnym rogu. Po kliknięciu użytkownik powinien zobaczyć listę języków do wyboru, a strona powinna zostać przetłumaczona.
  
## Uwagi:

**Języki**:
Warto zwrócić uwagę, że kod obsługuje jedynie język polski i angielski (pl, en), ale w prosty sposób można dodać inne języki, modyfikując zmienną includedLanguages w funkcji googleTranslateElementInit(). Możesz dodać więcej języków, oddzielając je przecinkami (np. 'pl,en,de,fr').

**Stylizacja**:
Kod zawiera przykładową stylizację, ale wygląd widgetu można łatwo dostosować do własnych potrzeb. Można zmienić kolory, rozmiar lub położenie widgetu w kodzie CSS.


### Kod

```php
/**
 * Widget automatycznego tłumaczenia strony
 */

function add_google_translate_widget() {
    ?>
    <style>
        /* Ukrywa belkę językową Google */
        .skiptranslate > iframe.skiptranslate {
            display: none !important; /* Ukrywa belkę Google */
        }
        body {
            top: 0px !important;
        }

        /* Stylizacja kontenera widgetu i przycisku */
        #translate_container {
            position: fixed; /* Umożliwia przyklejenie do boku */
            top: 20%; /* Ustal miejsce na ekranie */
            right: 20px; /* Ustal odległość od prawej krawędzi */
            z-index: 1000; /* Zapewnia, że widget jest na wierzchu */
            display: flex; /* Umożliwia układanie elementów w linii */
            flex-direction: row; /* Ustawia kierunek elementów na w poziomie */
            align-items: center; /* Wyrównuje przycisk i widget w pionie */
        }

        /* Stylizacja widgetu */
        #google_translate_element {
            background-color: white; /* Kolor tła widgetu */
            border: 1px solid #ccc; /* Opcjonalna ramka */
            padding: 10px; /* Opcjonalne odstępy */
            border-radius: 5px; /* Zaokrąglone rogi */
            box-shadow: 0 0 5px rgba(0,0,0,0.3); /* Opcjonalny cień */
            display: none; /* Ukrywa widget na początku */
            width: 140px; /* Szerokość widgetu */
            transition: margin-left 0.5s ease, opacity 0.5s ease; /* Płynne przejście marginesu i przezroczystości */
            margin-left: -220px; /* Ustawienie widgetu na lewo na początku */
            opacity: 0; /* Widget jest początkowo niewidoczny */
        }

        /* Stylizacja przycisku */
        #translate_button {
            background-color: #0098d6; /* Kolor tła przycisku */
            color: #ffffff; /* Kolor tekstu */
            border: none; /* Bez ramki */
            border-radius: 5px; /* Zaokrąglony przycisk */
            width: 60px; /* Szerokość przycisku */
            height: 52px; /* Wysokość przycisku */
            cursor: pointer; /* Wskaźnik kursora */
            display: flex; /* Umożliwia centrowanie ikony */
            justify-content: center; /* Wyśrodkowanie w poziomie */
            align-items: center; /* Wyśrodkowanie w pionie */
            box-shadow: 0 0 5px rgba(0,0,0,0.3); /* Opcjonalny cień */
            margin-right: 0; /* Mniejszy odstęp między przyciskiem a widgetem */
            font-size: 18px; /* Zmniejsza rozmiar tekstu przycisku */
        }
		
		 #translate_button:hover i {
           color: #7ecaf1; /* Kolor ikony po najechaniu */
        }

        /* Stylizacja ikony */
        #translate_button i {
            font-size: 26px; /* Jeszcze większy rozmiar ikony */
            margin: 0; /* Brak marginesów */
			color: #ffffff;
        }
    </style>

    <div id="translate_container">
        <button id="translate_button" onclick="toggleTranslateWidget()">
            <i class="fa fa-globe"></i> <!-- Ikona z Font Awesome -->
        </button>
        <div id="google_translate_element">
            <!-- Kontener dla Google Translate -->
        </div>
    </div>

    <script type="text/javascript">
        function googleTranslateElementInit() {
            new google.translate.TranslateElement({
                pageLanguage: 'pl', // język oryginalny strony
                includedLanguages: 'pl,en', // języki do wyboru
                layout: google.translate.TranslateElement.InlineLayout.SIMPLE,
                autoDisplay: false // nie wyświetlaj automatycznie
            }, 'google_translate_element');
        }

        // Ładowanie skryptu Google Translate
        (function() {
            var gt = document.createElement('script'); gt.type = 'text/javascript'; gt.async = true;
            gt.src = 'https://translate.google.com/translate_a/element.js?cb=googleTranslateElementInit';
            var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(gt, s);
        })();

        function toggleTranslateWidget() {
            var widget = document.getElementById('google_translate_element');
            
            if (widget.style.display === 'none' || widget.style.display === '') {
                widget.style.display = 'block'; // Pokaż widget
                setTimeout(function() {
                    widget.style.marginLeft = '0'; // Reset marginesu po krótkim czasie
                    widget.style.opacity = '1'; // Zwiększ przezroczystość
                }, 10);
            } else {
                widget.style.opacity = '0'; // Zmniejsz przezroczystość
                setTimeout(function() {
                    widget.style.display = 'none'; // Ukryj widget po zakończeniu animacji
                    widget.style.marginLeft = '-220px'; // Przesuń widget w lewo
                }, 500); // Czas, po którym ukrywamy widget
            }
        }
    </script>

    <!-- Link do Font Awesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css" integrity="sha384-k6RqeWeci5ZR/Lv4MR0sA0FfDOMmE1mXbE4w1h6F33qW+Z1DVu3xRbDA69AqJp4" crossorigin="anonymous">
    <?php
}
add_action('wp_footer', 'add_google_translate_widget');
