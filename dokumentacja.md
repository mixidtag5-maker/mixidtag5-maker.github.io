# DOKUMENTACJA TECHNICZNA KODU ŹRÓDŁOWEGO: KONSOLOWNIA.PL

Dokument ten zawiera analizę techniczną kodu źródłowego witryny Konsolownia.pl. Zawiera omówienie struktur HTML, arkuszy CSS oraz skryptów JavaScript, wyjaśniając krok po kroku działanie poszczególnych komponentów.

---

## 1. STRUKTURA PROJEKTU I PRZEPŁYW DANYCH

Strona opiera się na wielostronicowej architekturze (Multi-Page Application - MPA) bez użycia zewnętrznych bibliotek i frameworków (Vanilla JS & CSS).

*   `index.html` + `style.css` (Strona główna)
*   `akcesoria/index.html` + `akcesoria.css` (Pokaz spersonalizowanych padów i stojaków)
*   `wysylka/index.html` + `wysylka.css` (Krokowy flowchart nadawania paczek)
*   `kontakt/index.html` + `kontakt.css` (Sekcja kontaktowa z mapą i kafelkami social media)

---

## 2. PASEK NAWIGACYJNY (HEADER) I ZDARZENIA SCROLLOWANIA

### 2.1. Efekt przezroczystości i rozmycia tła przy scrollowaniu
W pliku `index.html` (oraz na pozostałych podstronach) skrypt JavaScript nasłuchuje zdarzenia przewijania strony (`scroll`) na obiekcie `window`:

```javascript
window.addEventListener('scroll', () => {
    const header = document.querySelector('.header-wrapper');
    if (window.scrollY > 10) {
        header.classList.add('scrolled');
    } else {
        header.classList.remove('scrolled');
    }
});
```

**Działanie kodu:**
1. Metoda `window.addEventListener('scroll')` wyzwala funkcję strzałkową za każdym razem, gdy użytkownik przewija stronę.
2. `window.scrollY` pobiera aktualną pozycję przewinięcia w pionie (w pikselach).
3. Jeżeli pozycja jest większa niż `10px`, do elementu `.header-wrapper` dynamicznie dodawana jest klasa `.scrolled`, w przeciwnym razie jest ona usuwana.

Klasa `.scrolled` modyfikuje zachowanie nagłówka w arkuszu `style.css`:

```css
.scrolled .header {
    background: rgba(255, 255, 255, 0.85);
    backdrop-filter: blur(12px);
    -webkit-backdrop-filter: blur(12px);
    box-shadow: 0 10px 40px rgba(0, 0, 0, 0.1);
}
```

**Działanie stylów CSS:**
*   `background: rgba(...)`: Ustawia tło o przezroczystości 85%.
*   `backdrop-filter: blur(12px)`: Wykonuje sprzętowe rozmycie warstwy znajdującej się *pod* nagłówkiem, dając nowoczesny efekt szklanej tafli (Glassmorphism).

---

### 2.2. Hamburger Menu na urządzeniach mobilnych
Kod HTML menu mobilnego oraz skrypt obsługi zdarzeń:

```html
<div class="hamburger" role="button" aria-label="Menu" tabindex="0">
    <span></span>
    <span></span>
    <span></span>
</div>
```

```javascript
const hamburger = document.querySelector('.hamburger');
const navLinks = document.querySelector('.nav-links');

hamburger.addEventListener('click', () => {
    navLinks.classList.toggle('active');
    hamburger.classList.toggle('active');
});
```

**Działanie kodu:**
1. Kliknięcie w element `.hamburger` wywołuje metodę `classList.toggle('active')`.
2. Klasa `.active` zmienia maksymalną wysokość menu (`max-height`), powodując jego płynne wysunięcie, oraz obraca linie hamburgera w znak "X".

Modyfikacja linii w `style.css` przy klasie `.active`:

```css
.hamburger.active span:nth-child(1) {
    transform: translateY(10px) rotate(45deg);
}
.hamburger.active span:nth-child(2) {
    opacity: 0;
}
.hamburger.active span:nth-child(3) {
    transform: translateY(-10px) rotate(-45deg);
}
```

*   `nth-child(1)` oraz `nth-child(3)` obracają się o 45 stopni w przeciwnych kierunkach i przesuwają w pionie za pomocą `translateY`, tworząc ramiona krzyżyka.
*   Środkowa linia (`nth-child(2)`) zostaje całkowicie ukryta za pomocą przezroczystości `opacity: 0`.

---

## 3. DYNAMICZNA STRONA GŁÓWNA: ANIMACJE BEZ UŻYCIA JS

### 3.1. Zmiana kolorów tła w pętli (CSS Keyframes)
Karta `.animated-bg-card` na stronie głównej płynnie zmienia kolory tła i odpowiadający mu cień (`box-shadow`) w nieskończonej pętli bez udziału JS:

```css
.animated-bg-card {
    background: none;
    animation: bgChange 8s infinite;
}

@keyframes bgChange {
    0%, 20% {
        background-color: #03349f;
        box-shadow: 0 20px 50px rgba(3, 52, 159, 0.5);
    }
    25%, 45% {
        background-color: #249e3a;
        box-shadow: 0 20px 50px rgba(36, 158, 58, 0.5);
    }
    50%, 70% {
        background-color: #1a1a1a;
        box-shadow: 0 20px 50px rgba(0, 0, 0, 0.5);
    }
    75%, 95% {
        background-color: #517a27;
        box-shadow: 0 20px 50px rgba(81, 122, 39, 0.5);
    }
    100% {
        background-color: #03349f;
        box-shadow: 0 20px 50px rgba(3, 52, 159, 0.5);
    }
}
```

**Działanie kodu:**
*   `animation: bgChange 8s infinite`: Uruchamia animację na 8 sekund w pętli nieskończonej (`infinite`).
*   Przedziały procentowe (np. `0%` do `20%`) utrzymują stały kolor przez pewien czas, po czym następuje płynne przejście tonalne (np. od `20%` do `25%`) do kolejnej barwy.

---

### 3.2. Karuzela przenikania obrazów (CSS Slider Crossfade)
Przenikanie zdjęć konsol wewnątrz karty "Spa" oparte jest na nakładaniu na siebie elementów absolutnych i sterowaniu ich przezroczystością w czasie:

```css
.gray-card .image-slider .product-img {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    opacity: 0;
    animation: sliderFade 8s infinite;
}

.gray-card .image-slider .product-img:nth-child(1) { animation-delay: 0s; }
.gray-card .image-slider .product-img:nth-child(2) { animation-delay: -6s; }
.gray-card .image-slider .product-img:nth-child(3) { animation-delay: -4s; }
.gray-card .image-slider .product-img:nth-child(4) { animation-delay: -2s; }

@keyframes sliderFade {
    0%, 20% { opacity: 1; }
    25%, 95% { opacity: 0; }
    100% { opacity: 1; }
}
```

**Działanie kodu:**
1. Wszystkie 4 grafiki konsol nakładają się na siebie w tym samym punkcie (`position: absolute`).
2. Animacja `sliderFade` sprawia, że przez 20% czasu trwania obraz jest w pełni widoczny (`opacity: 1`), a przez resztę czasu jest ukryty (`opacity: 0`).
3. Właściwość `animation-delay` z wartościami ujemnymi (np. `-6s`) przesuwa czas rozpoczęcia animacji dla poszczególnych elementów wstecz. Dzięki temu każdy obrazek pojawia się dokładnie w swojej sekwencji czasowej (co 2 sekundy).

---

## 4. SKRYPTY INTERAKTYWNYCH KARUZEL (AKCESORIA.CSS & JS)

### 4.1. Automatyczny slider z paskiem postępu synchronizowanym z JS
Slider na stronie `akcesoria/index.html` łączy logikę JS z animacją paska postępu w CSS.

#### Mechanizm synchronizacji (JS -> CSS):
Skrypt JS konfiguruje czas trwania slajdu i przekazuje go do arkusza stylów jako zmienną CSS:

```javascript
const SLIDE_DURATION = 4000; // 4000ms na slajd
document.querySelector('.slider-indicators').style.setProperty('--slide-duration', `${SLIDE_DURATION / 1000}s`);
```

#### Animacja paska w CSS:
Gdy dany wskaźnik otrzymuje klasę `.active`, pseudoelement `::after` napełnia się od lewej do prawej:

```css
.indicator.active {
    width: 52px;
}
.indicator.active::after {
    animation: indicator-fill var(--slide-duration, 4s) linear forwards;
}
@keyframes indicator-fill {
    from { width: 0%; }
    to   { width: 100%; }
}
```

#### Logika odświeżania animacji (Reflow Hack):
Aby pasek postępu zaczął ładować się od nowa przy przełączeniu slajdu, skrypt musi zmusić przeglądarkę do ponownego przeliczenia stylów układu graficznego (tzw. trigger reflow):

```javascript
function restartIndicatorAnimation(ind) {
    ind.classList.remove('active');
    void ind.offsetWidth; // Wymuszenie reflow (przerysowania elementu w przeglądarce)
    ind.classList.add('active');
}
```

*   `void ind.offsetWidth`: Jest to odczyt właściwości geometrycznej, który natychmiastowo przerywa optymalizację przeglądarki i wymusza natychmiastowe zresetowanie stanu animacji CSS przed ponownym nadaniem klasy `.active`.

---

### 4.2. Przewijana karuzela stojaków z wykrywaniem interakcji użytkownika
Skrypt realizuje automatyczne przewijanie w bok za pomocą metody `scrollTo`/`scrollBy` oraz wstrzymuje przewijanie podczas najechania myszką lub dotknięcia:

```javascript
const stojakiSlider = document.querySelector('.stojaki-slider');
let stojakiAutoTimer = null;

function startStojakiAutoScroll() {
    stojakiAutoTimer = setInterval(() => {
        const itemWidth = stojakiSlider.querySelector('.stojaki-item').clientWidth + 20; // 20 to gap/odstęp CSS

        // Jeśli osiągnięto koniec slidera
        if (stojakiSlider.scrollLeft + stojakiSlider.clientWidth >= stojakiSlider.scrollWidth - 10) {
            stojakiSlider.scrollTo({ left: 0, behavior: 'smooth' }); // Powrót na początek
        } else {
            stojakiSlider.scrollBy({ left: itemWidth, behavior: 'smooth' }); // Przesunięcie o 1 element
        }
    }, 5000);
}

// Obsługa interakcji: pauza przy najechaniu i wznowienie po opuszczeniu
stojakiSlider.addEventListener('mouseenter', () => clearInterval(stojakiAutoTimer));
stojakiSlider.addEventListener('mouseleave', () => startStojakiAutoScroll());
stojakiSlider.addEventListener('touchstart', () => clearInterval(stojakiAutoTimer));
stojakiSlider.addEventListener('touchend', () => startStojakiAutoScroll());
```

**Działanie kodu:**
1. `stojakiSlider.scrollLeft`: Pobiera aktualną pozycję przewinięcia w pikselach.
2. `stojakiSlider.scrollWidth`: Całkowita szerokość przewijanego obszaru.
3. Gdy użytkownik najeżdża na element (`mouseenter`), wywoływane jest `clearInterval(stojakiAutoTimer)`, co zapobiega nagłemu przewijaniu w trakcie przeglądania stojaków przez klienta. Po opuszczeniu obszaru (`mouseleave`), pętla czasowa jest uruchamiana na nowo.

---

## 5. STRZAŁKI POŁĄCZEŃ FLOWCHARTU W CSS (WYSYLKA.CSS)

### 5.1. Pozycjonowanie strzałek w układzie Desktop
W pliku `wysylka/index.html` kroki dostawy są połączone narysowanymi w CSS liniami. 

```html
<div class="desktop-arrows">
    <div class="line a1-h"></div>
    <div class="line a1-v"></div>
    <div class="arrow-head a1-head"></div>
    ...
</div>
```

Przykład rysowania strzałki łączącej Krok 1 (po lewej stronie ekranu) z Krokiem 2 (po prawej stronie):

```css
.line {
    position: absolute;
    background: #111;
}

/* Arrow 1: Box 1 -> Box 2 */
.a1-h {
    top: 9.25%;
    left: calc(40% + 15px);
    width: calc(35% - 15px + 2.5px);
    height: 5px;
    transform: translateY(-50%);
}

.a1-v {
    left: 75%;
    top: calc(9.25% - 2.5px);
    width: 5px;
    height: calc(9.25% - 15px - 14px + 2.5px + 4px);
    transform: translateX(-50%);
}

.arrow-head {
    position: absolute;
    width: 0;
    height: 0;
    border-left: 8px solid transparent;
    border-right: 8px solid transparent;
    border-top: 14px solid #111;
    z-index: 2;
    transform: translate(-50%, -100%);
}

.a1-head {
    top: calc(18.5% - 15px);
    left: 75%;
}
```

**Działanie kodu CSS:**
1. `.a1-h` (segment poziomy) jest pozycjonowany na wysokości `9.25%` wysokości kontenera rodzica i rozciąga się w prawo na szerokość obliczoną dynamicznie przez `calc()`.
2. `.a1-v` (segment pionowy) zaczyna się w miejscu zakończenia poziomego segmentu (`left: 75%`) i schodzi pionowo w dół.
3. `.arrow-head` tworzy grot strzałki za pomocą techniki obramowań CSS (trójkąt z zerową szerokością/wysokością elementu i przezroczystymi krawędziami bocznymi). Jest on przesuwany na sam dół pionowego segmentu.

---

### 5.2. Obsługa układu mobilnego (RWD Fallback dla strzałek)
W widoku mobilnym strzałki desktopowe są ukrywane, a kafelki układają się jeden pod drugim. Łączniki pionowe generowane są wtedy za pomocą pseudoelementu `::after` oraz klasy `.mobile-arrow-head`:

```css
@media (max-width: 768px) {
    .desktop-arrows {
        display: none;
    }

    .step-box:not(.box5)::after {
        content: '';
        position: absolute;
        top: calc(100% + 10px);
        left: 50%;
        transform: translateX(-50%);
        width: 4px;
        height: 25px;
        background: #111;
    }

    .step-box:not(.box5) .mobile-arrow-head {
        display: block;
        position: absolute;
        top: calc(100% + 35px);
        left: 50%;
        transform: translateX(-50%);
        border-left: 10px solid transparent;
        border-right: 10px solid transparent;
        border-top: 15px solid #111;
        z-index: 10;
    }
}
```

**Działanie kodu CSS:**
*   `::after` automatycznie dołącza pionową linię pod każdym kafelkiem (poza ostatnim `.box5`), pozycjonując ją na `50%` szerokości.
*   `.mobile-arrow-head` generuje trójkątny grot strzałki wskazujący w dół pod linią pionową, tworząc czytelny, pionowy diagram kroków.

---

## 6. SIATKA SOCIAL GRID I EFEKTY HOVER (KONTAKT.CSS)

Kafelki w dolnej części sekcji kontaktowej reagują na najechanie myszką poprzez jednoczesne uniesienie kafelka i powiększenie ikony:

```css
.contact-card {
    background: #f7f7f7;
    transition: transform 0.35s cubic-bezier(0.4, 0, 0.2, 1), box-shadow 0.35s ease;
}

.contact-grid-bottom .contact-card:hover {
    transform: translateY(-8px);
    box-shadow: 0 25px 60px rgba(0, 0, 0, 0.15);
}

.contact-grid-bottom .contact-card .card-icon svg {
    transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.contact-grid-bottom .contact-card:hover .card-icon svg {
    transform: scale(1.15);
}
```

**Działanie kodu CSS:**
*   `transition` z funkcją przejścia `cubic-bezier(...)` definiuje niestandardową, płynną fizykę ruchu (zamiast liniowej).
*   Podczas najechania (`:hover`), `transform: translateY(-8px)` przesuwa kafelek o 8px w górę na osi Y.
*   W tym samym momencie selektor potomka `.contact-card:hover .card-icon svg` powiększa wewnętrzną ikonę o 15% (`transform: scale(1.15)`), zapewniając atrakcyjną mikrointerakcję interfejsu.

---

## 7. DOSTĘPNOŚĆ (WCAG 2.1) NA POZIOMIE KODU

Witryna posiada pełne dostosowanie do nawigacji wyłącznie klawiaturą (np. dla osób z dysfunkcjami narządów ruchu lub słabowidzących).

### Obsługa klawisza Enter i Spacji na elementach sterujących
W plikach HTML dodano atrybut `tabindex="0"` do elementów, które nie są domyślnie klikalne przez klawiaturę (takie jak menu mobilne czy kropki karuzeli), co pozwala na skupienie na nich uwagi (focus). W plikach JS wdrożono nasłuchiwanie zdarzeń klawiatury:

```javascript
hamburger.addEventListener('keydown', (e) => {
    if (e.key === 'Enter' || e.key === ' ') {
        e.preventDefault(); // Powstrzymanie domyślnego przewijania strony przy spacji
        navLinks.classList.toggle('active');
        hamburger.classList.toggle('active');
    }
});
```

**Działanie kodu:**
1. Zdarzenie `keydown` przechwytuje naciśnięcia klawiszy, gdy element `.hamburger` jest zaznaczony (ma na sobie focus).
2. Warunek `e.key === 'Enter' || e.key === ' '` sprawdza, czy naciśnięto klawisz Enter lub Spację.
3. Metoda `e.preventDefault()` zatrzymuje domyślne zachowanie przeglądarki (np. przewijanie okna w dół po naciśnięciu spacji), aby klawisz działał wyłącznie jako przycisk menu.
