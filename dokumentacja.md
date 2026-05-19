# SKRÓCONA DOKUMENTACJA TECHNICZNA: KONSOLOWNIA.PL

Kompaktowe podsumowanie architektury i kluczowych rozwiązań technicznych wdrożonych w projekcie Konsolownia.pl (Vanilla HTML, CSS i JS).

---

## 1. ARCHITEKTURA I STRUKTURA PROJEKTU
Projekt zrealizowano jako klasyczną witrynę wielostronicową (**MPA - Multi-Page Application**):
*   `index.html` (style.css) – Strona główna z sekcjami produktowymi i animowanymi kartami.
*   `akcesoria/index.html` (akcesoria.css) – Dynamiczna galeria spersonalizowanych padów i karuzela stojaków.
*   `wysylka/index.html` (wysylka.css) – Interaktywny, responsywny flowchart procesu wysyłki.
*   `kontakt/index.html` (kontakt.css) – Formularz kontaktowy, mapa oraz kafelki społecznościowe.

---

## 2. PASEK NAWIGACYJNY (HEADER) I RWD

### 2.1. Efekt Glassmorphism (Scroll)
Dodanie klasy `.scrolled` do nagłówka po przewinięciu powyżej `10px` (`window.scrollY > 10`) z wykorzystaniem sprzętowego rozmycia warstwy tła w CSS:
```css
.scrolled .header {
    background: rgba(255, 255, 255, 0.85);
    backdrop-filter: blur(12px);
    -webkit-backdrop-filter: blur(12px);
}
```

### 2.2. Hamburger Menu & Animacja "X"
Obsługa zdarzenia `click` na `.hamburger` przełącza klasę `.active` dla menu i przycisku, wyzwalając płynne przekształcenie linii w znak "X":
```css
.hamburger.active span:nth-child(1) { transform: translateY(10px) rotate(45deg); }
.hamburger.active span:nth-child(2) { opacity: 0; }
.hamburger.active span:nth-child(3) { transform: translateY(-10px) rotate(-45deg); }
```

---

## 3. WYDAJNE ANIMACJE W CSS (BEZ JS)

*   **Pętla kolorów tła (`@keyframes bgChange`):** Płynne, cykliczne przejścia barwne i cienie (`box-shadow`) realizowane całkowicie w CSS, odciążające wątek główny JS.
*   **Karuzela przenikania (Crossfade Slider):** Równoległe nałożenie grafik (`position: absolute`) i cykliczna modulacja `opacity` za pomocą przesuniętych w czasie animacji z ujemnymi opóźnieniami (`animation-delay`), co zapewnia płynną sekwencję bez użycia skryptów.

---

## 4. SKRYPTY INTERAKTYWNYCH KARUZEL (AKCESORIA)

### 4.1. Karuzela Padów z Paskiem Postępu
1.  **Zmienne CSS (Custom Properties):** Skrypt JS konfiguruje czas slajdu i dynamicznie przekazuje go do CSS (`--slide-duration`), sterując animacją paska postępu (`@keyframes indicator-fill`).
2.  **Trigger Reflow (Hack przerysowania):** Aby pasek ładował się od nowa przy ręcznym przełączeniu slajdu, skrypt wymusza natychmiastowe odświeżenie geometrii elementu przed ponownym nadaniem klasy `.active`:
    ```javascript
    ind.classList.remove('active');
    void ind.offsetWidth; // Wymuszenie natychmiastowego reflow w przeglądarce
    ind.classList.add('active');
    ```

### 4.2. Karuzela Stojaków z Autoodtwarzaniem i Autopauzą
Automatyczne przewijanie poziome (`scrollBy` / `scrollTo`) w pętli `setInterval`.
*   **Autopauza interakcji:** Rejestracja zdarzeń `mouseenter`/`touchstart` wstrzymuje automatyczne przewijanie (`clearInterval`), a `mouseleave`/`touchend` wznawia je. Zapobiega to niespodziewanemu ruchowi podczas przeglądania oferty przez klienta.

---

## 5. FLOWCHART PROCESU WYSYŁKI (RWD)

### 5.1. Strzałki Łączące (Układ Desktop)
Elementy łączące tworzone są całkowicie za pomocą absolutnie pozycjonowanych divów z szerokościami i wysokościami wyliczanymi za pomocą `calc()`. Trójkątne groty strzałek (`.arrow-head`) generowane są z użyciem transparentnych obramowań (`border`).

### 5.2. Łączniki Pionowe (Układ Mobilny)
Na urządzeniach mobilnych (`max-width: 768px`) strzałki desktopowe są ukrywane, a kroki układają się pionowo. Łączniki pionowe i groty generowane są dynamicznie przez pseudoelementy `::after` pod każdym kafelkiem (poza ostatnim) oraz klasę `.mobile-arrow-head`.

---

## 6. MIKROINTERAKCJE HOVER (KONTAKT)
Kafelki kontaktowe i ikony SVG reagują na najechanie myszką, korzystając z niestandardowej fizyki przejść `cubic-bezier`:
*   Uniesienie kafelka: `transform: translateY(-8px)` i rozmycie cienia.
*   Powiększenie ikony SVG o 15%: `transform: scale(1.15)`.

---

## 7. DOSTĘPNOŚĆ (WCAG 2.1)

Witryna w pełni wspiera sterowanie za pomocą samej klawiatury:
*   **Nawigacja tabulacją:** Dodano `tabindex="0"` dla niestandardowych elementów interaktywnych.
*   **Obsługa klawiszy Enter / Spacja:** Skrypty nasłuchują zdarzeń `keydown` i przy wyzwoleniu wywołują odpowiednią akcję z użyciem `e.preventDefault()`, zapobiegając domyślnemu przewijaniu strony spacją.
