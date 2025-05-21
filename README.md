# clean-code-angular
**Angular Clean Code Guidelines**



---

### Grundprinzipien

Diese allgemeinen Prinzipien bilden das Fundament für guten Code, unabhängig von der verwendeten Technologie.

1.  **Bedeutungsvolle Namen (Meaningful Names):**
    *   Wähle Klassennamen, Variablennamen, Methodennamen, Dateinamen etc., die klar und präzise ihre Absicht oder ihren Inhalt kommunizieren und keine Mehrdeutigkeiten zulassen.
    *   Vermeide Abkürzungen, die nicht allgemein verständlich sind (z.B. `prodList` statt `products`).
    *   Vermeide unnötige Codierung im Namen (z.B. `m_` oder `I` für Member/Interface).
    *   **Gutes Beispiel:**
        ```typescript
        // Anstatt:
        // const ud = getUsersData();
        // function procUsr(usr) { ... }

        // Besser:
        const userData = fetchUsersData();
        function processUser(user) { /* ... */ }
        ```
    *   **Angular-Spezifisch:**
        *   Benutze etablierte Suffixe (z.B. `.component.ts`, `.service.ts`, `.pipe.ts`, `.directive.ts`, `.guard.ts`, `.resolver.ts`).
        *   Für Komponenten-Selektoren, verwende ein konsistentes Präfix (z.B. `app-`, `lib-`) und `kebab-case` (z.B. `app-feature-name`).

2.  **Single Responsibility Principle (SRP):**
    *   Jede Klasse, jedes Modul und jede Funktion sollte nur *einen* klar definierten Grund zur Änderung haben.
    *   Methoden sollten kurz sein, fokussiert und idealerweise nur eine klar definierte Aufgabe erfüllen. Zerlege komplexe Methoden in kleinere, logische Einheiten.
    *   Vermeide lange Parameterlisten; ziehe ggf. Konfigurationsobjekte oder Interfaces in Betracht.
    *   **Beispiel für Methoden-SRP:**
        ```typescript
        // Schlecht: Methode tut zu viel
        function processOrder(order: Order, isValid: boolean): void {
          if (isValid) {
            // Validierung (wenn nicht schon vorher passiert)
            // Speichern der Bestellung
            // Benachrichtigung an User
            // Logik für Lagerbestand aktualisieren
          }
        }

        // Besser: Aufteilen in kleinere Methoden
        function processOrder(order: Order): void {
          validateOrder(order);
          saveOrder(order);
          notifyUser(order.user);
          updateInventory(order.items);
        }
        ```
    *   **Angular-Spezifisch:**
        *   Lifecycle-Hooks (z.B. `ngOnInit`, `ngOnChanges`) sollten nicht mit zu viel Logik überladen werden. Lagere komplexe Logik in private Methoden oder Services aus.

3.  **Kommentare sind der letzte Ausweg (Comments are a Last Resort):**
    *   Code sollte selbsterklärend sein. Wenn ein Kommentar notwendig ist, um zu erklären, *was* der Code tut, sollte der Code überarbeitet werden.
    *   Kommentare sind akzeptabel, um zu erklären, *warum* etwas auf eine bestimmte Art und Weise gemacht wurde (z.B. eine Geschäftsentscheidung, eine Kompromisslösung, eine Workaround für einen Bug).
    *   **Kein auskommentierter Code:** Entferne auskommentierten Code sofort. Dafür gibt es die Versionskontrolle. Das Belassen von auskommentiertem Code ist eine "Abomination".
    *   Vermeide veraltete oder redundante Kommentare.

4.  **Konsistente Formatierung (Consistent Formatting):**
    *   Nutze Tools wie Prettier und einen Linter (ESLint mit Angular-spezifischen Plugins), um einen einheitlichen Code-Stil im Team durchzusetzen und Abweichungen automatisch zu erkennen. Konsistenz ist entscheidend für gute Lesbarkeit und Zusammenarbeit.
    *   **Dateigröße als Heuristik:** Dateien sollten idealerweise unter 400 Codezeilen bleiben. Überschreitungen dieses Wertes sind oft ein Indikator dafür, dass das Single Responsibility Principle verletzt wird oder zu viele Verantwortlichkeiten an einem Ort vereint sind. Dies ist ein Richtwert und keine starre Grenze.

5.  **Robuste Fehlerbehandlung (Robust Error Handling):**
    *   Nutze Exceptions für unerwartete Fehlerzustände und behandle sie angemessen. Gib keine `null`-Werte oder generische Fehlercodes zurück, wo spezifische Exceptions angebrachter wären.
    *   **Angular-Spezifisch:**
        *   Nutze RxJS-Fehlerbehandlungsoperatoren (`catchError`, `retry`, `finalize`) in Observables für eine resiliente Datenflusssteuerung.
        *   Implementiere einen globalen `ErrorHandler` für eine zentrale Fehlerprotokollierung und -anzeige.

6.  **Vermeide Duplikate (Don't Repeat Yourself - DRY):**
    *   Wiederholter Code ist ein "Code Smells" und sollte in wiederverwendbare Funktionen, Services, Komponenten, Pipes oder Direktiven extrahiert werden. Das Ziel ist, dass jedes Stück Wissen nur einmal existiert.
    *   **Beispiel für DRY:**
        ```typescript
        // Schlecht: Wiederholter API-Aufruf
        loadProducts() {
          this.productService.getProducts().subscribe(/* ... */);
        }

        loadServices() {
          this.serviceService.getServices().subscribe(/* ... */);
        }

        // Besser: Extrahiere gemeinsame Logik
        loadData<T>(service: DataService<T>, targetList: T[]): void {
          service.getEntities().subscribe(data => {
            targetList = data;
          });
        }
        ```

7.  **Schreibe Tests (Write Tests):**
    *   Guter Code ist testbarer Code. Unit-Tests und Komponenten-Tests sind essentiell für die Sicherstellung der Korrektheit und ermöglichen sicheres Refactoring.
    *   Tests dokumentieren das erwartete Verhalten des Codes. Sie sollten schnell, unabhängig und fokussiert sein.
    *   **Schnelle Tests sind entscheidend:** Tests, die langsam sind, werden oft nicht ausgeführt. Sorge dafür, dass deine Testsuite schnell läuft, damit du sie häufig ausführen kannst.
    *   Konzentriere dich auf das Testen von Randbedingungen und Fehlerfällen.

---

### Angular-Spezifische Clean Code Regeln

Diese Regeln sind auf die Angular-Architektur zugeschnitten und bauen auf den allgemeinen Prinzipien auf.

#### Core Architecture:

*   **Standalone Components:** Für neuen Code sind Komponenten, Direktiven und Pipes standardmäßig Standalone-APIs. Das `standalone: true` Flag sollte in Angular v17+ und höher in neuem Code weggelassen werden, da es implizit ist und die Angulardatei vergrößert.
*   **Strong Typing:** TypeScript-Typen, Interfaces und Models gewährleisten Typsicherheit und verbessern die Lesbarkeit sowie Wartbarkeit im gesamten Codebase. Nutze `strict` Mode in `tsconfig.json`.
*   **Single Responsibility:** Jede Komponente und jeder Service hat eine einzelne, klar definierte Verantwortlichkeit. Services sollten Datenhaltung und Business-Logik übernehmen, Komponenten die Präsentationslogik.
*   **Rule of One:** Dateien konzentrieren sich auf ein einzelnes Konzept oder eine Funktionalität (z.B. eine Komponente pro Datei).
*   **Reactive State:** Signals bieten ein reaktives und effizientes State-Management. Bevorzuge sie für die Verwaltung des lokalen Zustands.

#### Komponenten (Components):

*   **Namenskonvention:** Komponenten-Dateien folgen einem konsistenten Namensschema: `feature-name.component.ts`. Selektoren `app-feature-name`.
*   **Template- und Style-Extraktion:** Nicht-triviale Templates gehören in separate `.html`-Dateien. Styles gehören in separate `.css` / `.scss`-Dateien.
*   **Signal-basierte Inputs:** Komponenten verwenden die `input()`-Funktion für Inputs anstelle des älteren `@Input()`-Dekorators (ohne `transform`):
    ```typescript
    // Aktuelles Pattern
    readonly value = input(0); // Erstellt InputSignal
    // Legacy Pattern (nicht mehr verwenden)
    // @Input() value = 0;
    ```
*   **Required Inputs:** Die `input.required()`-Funktion markiert Inputs als verpflichtend, was die Typsicherheit und Klarheit erhöht:
    ```typescript
    readonly value = input.required<number>();
    ```
*   **Input-Transformationen:** Verwende `input()` mit dem `transform`-Option, um Input-Werte automatisch zu konvertieren:
    ```typescript
    readonly disabled = input(false, { transform: booleanAttribute });
    readonly value = input(0, { transform: numberAttribute });
    ```
*   **Two-Way Binding:** `model()`-Inputs ermöglichen Two-Way-Binding auf moderne Weise:
    ```typescript
    readonly value = model(0); // Erstellt ein Model-Input mit Change-Propagation
    increment(): void {
      this.value.update(v => v + 1); // Model-Werte werden mit .set() oder .update() aktualisiert
    }
    ```
*   **Input-Aliase:** Nutze die `alias`-Option für alternative Input-Namen, wenn erforderlich:
    ```typescript
    readonly value = input(0, { alias: "sliderValue" });
    ```
*   **Change Detection Strategy:** Komponenten verwenden `OnPush` Change Detection für bessere Performance und um unerwartetes Verhalten zu vermeiden.
*   **Lazy Loading:** Die `@defer`-Direktive oder Router-Konfigurationen laden schwere Komponenten oder Features bei Bedarf, um die initiale Ladezeit zu verbessern.
*   **Moderner Control Flow:** Templates verwenden `@if`, `@for`, `@switch` anstelle der älteren strukturellen Direktiven (`*ngIf`, `*ngFor`, `*ngSwitch`).
*   **State Representation:** Komponenten sollten ihre Loading- und Error-States klar implementieren und im Template entsprechend visualisieren.
*   **Derived State:** Die `computed()`-Funktion wird verwendet, um abgeleitete Zustände effizient zu berechnen.

#### Services:

*   **Function-Based DI:** Verwende die `inject()`-Funktion für Dependency Injection anstelle der Konstruktor-Injection in neuem Code, wenn kein Konstruktor für andere Zwecke benötigt wird:
    ```typescript
    import { inject } from "@angular/core";
    import { HttpClient } from "@angular/common/http";

    export class MyService {
      private readonly http = inject(HttpClient);
      // ...
    }
    ```
*   **Service Declaration:** Services verwenden den `@Injectable()`-Dekorator mit `providedIn: 'root'` für Singletons, es sei denn, sie sollen scoped sein.
*   **Focused Responsibilities:** Services konzentrieren sich auf spezifische Aufgaben (z.B. Datenzugriff, Business-Logik, State-Management). Überlade Services nicht mit zu vielen Verantwortlichkeiten.
*   **Service Contracts:** Interfaces definieren Service-Verträge, um die Kopplung zu reduzieren und die Testbarkeit zu verbessern.

#### RxJS & Signals:

*   **Vermeide manuelle Subscriptions wo möglich:** Nutze den `async` Pipe in Templates oder konvertiere zu Signals mit `toSignal()`.
*   **Memory Leaks vermeiden:** Unsubscribe immer von Observables, die nicht durch den `async` Pipe oder `takeUntilDestroyed` (ab Angular 16+) verwaltet werden.
*   **Signals als primäre State-Management-Lösung:**
    *   **Lokaler State:** `Writable Signals` mit `signal()` verwalten lokalen Komponenten-State.
    *   **Abgeleiteter State:** `Computed Signals` mit `computed()` berechnen abgeleiteten State.
    *   **Seiteneffekte:** Die `effect()`-Funktion behandelt Seiteneffekte, die keine direkte Zustandsänderung sind.
*   **Signal-Konvertierung:** Die `toSignal()`- und `toObservable()`-Funktionen ermöglichen Interoperabilität zwischen RxJS-Observables und Signals.

#### Direktiven und Pipes:

*   **Attribut-Direktiven:** Direktiven behandeln Präsentationslogik und DOM-Interaktionen ohne eigene Templates.
*   **Host Property:** Das `host`-Property im `@Directive()`-Dekorator verwaltet Host-Bindings und -Listener auf moderne und performante Weise:
    ```typescript
    @Directive({
      selector: '[appHighlight]',
      host: {
        '[class.highlighted]': 'isHighlighted',
        '[style.color]': 'highlightColor',
        '(click)': 'onClick($event)'
      }
    })
    export class HighlightDirective {
      isHighlighted = false;
      highlightColor = 'yellow';

      onClick(event: Event): void {
        console.log('Element clicked!', event);
      }
    }
    ```
*   **Pure Pipes:** Pipes sind wenn möglich "pure" (`pure: true` ist Standard) für bessere Performance, da sie nur bei Änderungen der Input-Werte neu berechnet werden.

#### Module & Standalone:

*   **Standalone als Standard:** Für neue Projekte und beim Refactoring sollten Standalone Components, Directives und Pipes konsequent verwendet werden.
*   **Keine Legacy-Module:** Vermeide die Erstellung von `NgModules` für neue Features. Migriere bestehende Module zu Standalone-APIs, wo immer praktikabel.
*   **Gezielte Importe:** Importiere nur die tatsächlich benötigten Standalone-APIs in Standalone-Komponenten. Vermeide generische Importe wie `CommonModule` oder `RouterModule` in Standalone-Komponenten, es sei denn, deren gesamte Exportliste wird benötigt.
    ```typescript
    // Schlecht (in einer Standalone-Komponente):
    // imports: [CommonModule, RouterModule]

    // Besser (wenn nur NgIf und RouterLink benötigt werden):
    import { NgIf } from '@angular/common';
    import { RouterLink } from '@angular/router';

    @Component({
      standalone: true,
      selector: 'app-my-component',
      template: `<div *ngIf="show">...</div><a routerLink="/detail">...</a>`,
      imports: [NgIf, RouterLink] // Nur das importieren, was wirklich benötigt wird
    })
    export class MyComponent {
      show = true;
    }
    ```
*   **Lazy Loading:** Nutze Lazy Loading (mit `loadComponent` für Standalone-Routen) um die initiale Ladezeit der Anwendung zu verbessern und Bundles klein zu halten.

#### Template Patterns:

*   **Moderner Control Flow:** Verwende die neue Angular Control Flow Syntax: `@if`, `@for`, `@switch` in Templates. Vermeide die Legacy-Strukturdirektiven (`*ngIf`, `*ngFor` oder `*ngSwitch`).
    ```html
    <!-- Anstatt: -->
    <!-- <div *ngIf="isLoading">Loading...</div> -->
    <!-- <li *ngFor="let item of items">...</li> -->
    <!-- <div [ngSwitch]="status">...</div> -->

    <!-- Besser (moderner Control Flow): -->
    @if (isLoading) {
      <div>Loading...</div>
    }
    @for (item of items; track item.id) {
      <li>{{ item.name }}</li>
    } @empty {
      <p>No items found.</p>
    }
    @switch (status) {
      @case ("success") { <div>Success!</div> }
      @case ("error") { <div>Error!</div> }
      @default { <div>Pending...</div> }
    }
    ```
*   **Referenzierung bedingter Ergebnisse:** Bei Verwendung von `@if` referenziere das Ergebnis mit dem `as`-Keyword, z.B. `@if (user(); as u) { ... }`. Dies ist das empfohlene Muster für den Zugriff auf den Wert innerhalb des Blocks.
    ```html
    @if (currentUser(); as user) {
      <p>Willkommen, {{ user.name }}!</p>
    } @else {
      <p>Bitte melden Sie sich an.</p>
    }
    ```

#### Formulare:

*   **Typed Forms:** Verwende immer streng typisierte Reactive Forms, indem du ein Interface für die Formularwerte definierst und `FormGroup<MyFormType>`, `FormBuilder.group<MyFormType>()` und `FormControl<T>()` verwendest.
    ```typescript
    interface UserForm {
      firstName: string;
      lastName: string;
      email: string;
    }

    // ...
    const userForm = new FormGroup<UserForm>({
      firstName: new FormControl('', { nonNullable: true }),
      lastName: new FormControl('', { nonNullable: true }),
      email: new FormControl('', { nonNullable: true, validators: [Validators.email] })
    });

    // Oder mit FormBuilder
    // constructor(private fb: FormBuilder) {
    //   this.userForm = this.fb.group<UserForm>({ ... });
    // }
    ```
*   **Non-Nullable Controls:** Bevorzuge `nonNullable: true` für Controls, um `null`-Probleme zu vermeiden und die Typsicherheit zu verbessern.
*   **Reactive Forms für komplexe Szenarien:** Bevorzuge Reactive Forms für komplexe Formulare mit dynamischer Validierung, komplexer Logik oder in Szenarien, bei denen der Formularstatus programmatisch manipuliert werden muss.
*   **Template-Driven Forms für einfache Fälle:** Geeignet für sehr einfache Formulare ohne komplexe Validierungsanforderungen.

#### Styling:

*   **Komponenten-Kapselung:** Komponenten verwenden Scoped Styles (`ViewEncapsulation.Emulated` ist Standard), um die Isolation von Styles zu gewährleisten.
*   **CSS-Methodologie: BEM**
    BEM steht für **Block**, **Element** und **Modifier** und ist eine beliebte Namenskonvention für CSS-Klassen, die darauf abzielt, die Wiederverwendbarkeit und Wartbarkeit von CSS zu verbessern.

    *   **Block (`.block`):** Ein eigenständiges, wiederverwendbares UI-Element. Das ist der Hauptteil deiner Komponente.
        *   **Beispiel:** Ein Button, ein Header, eine Card.
        *   **CSS:** `.card`

    *   **Element (`.block__element`):** Ein Teil eines Blocks, der keine eigenständige Bedeutung hat und semantisch an seinen Block gebunden ist.
        *   **Beispiel:** Der Titel oder Text innerhalb einer Card.
        *   **CSS:** `.card__title`, `.card__text`

    *   **Modifier (`.block--modifier` oder `.block__element--modifier`):** Ein Flag für einen Block oder ein Element, das seine Darstellung oder sein Verhalten ändert.
        *   **Beispiel:** Eine Card in einer "Featured"-Version, oder ein Button in einer "Disabled"-Version.
        *   **CSS:** `.card--featured`, `.button--disabled`

    **Vorteile von BEM (und Relevanz für Angular):**
    *   **Modularität und Wiederverwendbarkeit:** Jede Klasse ist klar definiert und kann unabhängig verwendet werden. In Angular bedeutet dies, dass deine Komponenten-Styles in sich geschlossen und leicht zwischen Komponenten verschoben oder wiederverwendet werden können.
    *   **Klarheit:** Sofort ersichtlich, welche Rolle ein CSS-Selektor spielt. Du siehst sofort, ob es sich um eine eigenständige Komponente (Block), einen Teil davon (Element) oder eine Variation (Modifier) handelt.
    *   **Keine Kaskadenprobleme:** BEM-Klassen sind spezifisch genug, um Kaskadenprobleme zu minimieren, da sie selten überschrieben werden müssen. In Angular, wo Komponenten-Kapselung durch Shadow DOM oder Emulated Encapsulation gegeben ist, unterstützt BEM zusätzlich die Lesbarkeit und Organisation, auch wenn die globale Kaskade weniger ein Problem darstellt.

    **BEM Beispiel in Angular:**
    Nehmen wir an, du hast eine `ProductCardComponent`.

    ```html
    <!-- product-card.component.html -->
    <div class="product-card">
      <img class="product-card__image" [src]="product.imageUrl" alt="Product Image">
      <h3 class="product-card__title">{{ product.name }}</h3>
      <p class="product-card__price">{{ product.price | currency }}</p>
      <button class="product-card__button product-card__button--primary">In den Warenkorb</button>
      <button class="product-card__button product-card__button--secondary">Details</button>
    </div>
    ```

    ```scss
    /* product-card.component.scss */
    .product-card {
      border: 1px solid #ccc;
      padding: 16px;
      margin: 16px;
      display: flex;
      flex-direction: column;
      align-items: center;
      box-shadow: 2px 2px 5px rgba(0,0,0,0.1);

      &__image {
        width: 100px;
        height: 100px;
        object-fit: cover;
        margin-bottom: 8px;
      }

      &__title {
        font-size: 1.2em;
        margin-bottom: 4px;
        color: #333;
      }

      &__price {
        font-weight: bold;
        color: #007bff;
        margin-bottom: 12px;
      }

      &__button {
        background-color: #007bff;
        color: white;
        border: none;
        padding: 8px 16px;
        cursor: pointer;
        border-radius: 4px;
        margin-top: 8px;

        &--primary {
          background-color: #28a745; // Grün
        }

        &--secondary {
          background-color: #6c757d; // Grau
        }

        &:hover {
          opacity: 0.9;
        }
      }
    }
    ```

*   **Theming:** Implementiere ein konsistentes Farbsystem und Theming-Strategie, um ein einheitliches visuelles Design zu gewährleisten.

#### Testen:

*   **Test Coverage:** Strebe eine hohe Testabdeckung an, um die Qualität und Zuverlässigkeit der Codebasis sicherzustellen.
*   **Unit Tests:** Fokussierte Unit-Tests überprüfen einzelne Services, Pipes und Komponenten in Isolation.
*   **Komponenten-Testing:** Nutze das Angular `TestBed` und `Component Harnesses` für effektive und robuste Komponenten-Tests.
*   **Test-Organisation:** Organisiere Tests nach dem AAA-Muster (Arrange, Act, Assert) für Lesbarkeit und Wartbarkeit.
    *   **Beispiel AAA-Muster:**
        ```typescript
        // product.service.spec.ts
        describe('ProductService', () => {
          let service: ProductService;
          let httpTestingController: HttpTestingController;

          beforeEach(() => {
            TestBed.configureTestingModule({
              imports: [HttpClientTestingModule],
              providers: [ProductService]
            });
            service = TestBed.inject(ProductService);
            httpTestingController = TestBed.inject(HttpTestingController);
          });

          afterEach(() => {
            httpTestingController.verify(); // Stellt sicher, dass keine ausstehenden Anfragen vorhanden sind
          });

          it('should fetch products (Arrange, Act, Assert)', () => {
            // Arrange
            const mockProducts = [{ id: 1, name: 'Test Product' }];

            // Act
            service.getProducts().subscribe(products => {
              // Assert
              expect(products).toEqual(mockProducts);
            });

            const req = httpTestingController.expectOne('/api/products');
            expect(req.request.method).toEqual('GET');
            req.flush(mockProducts); // Simuliere die API-Antwort
          });
        });
        ```
*   **E2E-Testing:** Playwright oder Cypress eignen sich hervorragend für End-to-End-Tests, komplett mit Fixtures und Test-Isolation, um reale Benutzerinteraktionen abzubilden. Wichtig ist, dass diese Tests schnell laufen.

#### Performance:

*   **OnPush Change Detection:** Komponenten verwenden standardmäßig die `OnPush` Change Detection Strategy, um unnötige Überprüfungen zu minimieren.
*   **Lazy Loading:** Routen und nicht-essentielle Komponenten werden lazy geladen, um die initiale Bundle-Größe und Ladezeit zu reduzieren.
*   **Virtual Scrolling:** Implementiere Virtual Scrolling für lange Listen, um die Rendering-Performance zu optimieren.
*   **Bundle Size Monitoring & Optimierung:** Überwache und optimiere regelmäßig die Bundle-Größe mit Tools wie Webpack Bundle Analyzer.
*   **Server-Side Rendering (SSR) / Hydration:** Ziehe SSR/Hydration in Betracht, um die initiale Ladeperformance und SEO zu verbessern.

#### Sicherheit:

*   **XSS-Prävention:** Angulars integrierte Sanitization schützt vor Cross-Site Scripting (XSS). Benutzereingaben sollten immer entsprechend sanitisiert werden.
*   **CSRF-Schutz:** Implementiere Cross-Site Request Forgery (CSRF)-Tokens über HTTP-Interceptoren, um Formulare abzusichern.
*   **Content Security Policy (CSP):** Setze CSP-Header, um die Quellen von Inhalten zu beschränken und das Risiko von Code-Injection-Angriffen zu minimieren.
*   **Authentifizierung:** Verwende sichere und bewährte Authentifizierungsmechanismen (z.B. OAuth 2.0, OpenID Connect) für den Benutzerzugriff.
*   **Autorisierung:** Implementiere feingranulare Autorisierungsprüfungen (z.B. Guards, Direktiven, Backend-Checks), um den Zugriff auf Ressourcen und Funktionen zu kontrollieren.

#### Barrierefreiheit (Accessibility - a11y):

*   **ARIA-Attribute:** Verwende korrekt ARIA-Attribute, um Rich-Internet-Anwendungen für Benutzer mit Assistenztechnologien zugänglich zu machen.
*   **Tastaturnavigation:** Stelle sicher, dass alle interaktiven Elemente vollständig mit der Tastatur bedienbar sind und der Fokus logisch geführt wird.
*   **Farbkontrast:** UI-Elemente sollten ausreichende Farbkontrastverhältnisse gemäß WCAG-Standards aufweisen.
*   **Screenreader-Kompatibilität:** Teste Komponenten mit gängigen Screenreadern, um eine korrekte Ausgabe zu gewährleisten.
*   **Alternativer Text:** Bilder und nicht-textliche Inhalte enthalten aussagekräftige Alt-Texte.

---

### Tools zur Unterstützung von Clean Code:

*   **ESLint:** Mit Plugins wie `@angular-eslint/eslint-plugin` für Angular-spezifische Linting-Regeln, um Coding-Standards und Best Practices durchzusetzen.
*   **Prettier:** Für automatische Code-Formatierung. Integriere es in den Pre-Commit-Hook, um sicherzustellen, dass der gesamte Code konsistent ist.
*   **Angular CLI:** Generiert Code nach Best Practices und hilft, die Projektstruktur beizubehalten und Boilerplate-Code zu reduzieren.
*   **Code Reviews:** Unverzichtbar, um die Code-Qualität im Team sicherzustellen, Wissen zu teilen, Probleme frühzeitig zu erkennen und die Einhaltung dieser Richtlinien zu fördern. Code Reviews sind ein wesentlicher Bestandteil eines Clean-Code-Umfelds.
