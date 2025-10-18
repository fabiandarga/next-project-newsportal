# Übung 2: Seiten und Navigation erstellen

## Ziel

In dieser Übung lernst du das dateibasierte Routing-System von Next.js kennen. Du wirst eine "Über uns"-Seite erstellen und eine Navigation hinzufügen, um zwischen den Seiten zu wechseln.

## Zeit: 30 Minuten

## Schritt 1: Über uns-Seite erstellen

In Next.js entspricht jeder Ordner einer Route, und die `page.js` Datei in diesem Ordner definiert den Inhalt dieser Seite.

1. Erstelle einen neuen Ordner `about` im `app`-Verzeichnis:

    ```
    app/about/
    ```

2. Erstelle eine neue Datei `page.js` im `about`-Ordner

3. Füge folgenden Code in die neue Datei ein:

```jsx
export default function About() {
    return (
        <main className="p-8">
            <h1 className="text-2xl font-bold mb-4">Über uns</h1>
            <p className="mb-4">
                Willkommen auf unserem News-Portal! Wir sind ein Team von engagierten Redakteuren, die täglich die
                neuesten Nachrichten für dich zusammenstellen.
            </p>
            <p className="mb-4">
                Unser Ziel ist es, dir aktuelle und relevante Informationen zu bieten, damit du immer auf dem neuesten
                Stand bleibst.
            </p>
            <p>
                Dieses Portal wurde mit Next.js erstellt, einem modernen React-Framework für produktionsreife
                Webanwendungen.
            </p>
        </main>
    );
}
```

4. Speichere die Datei und teste die neue Seite, indem du im Browser zu [http://localhost:3000/about](http://localhost:3000/about) navigierst

## Schritt 2: Navigationskomponente erstellen

Jetzt erstellen wir eine Navigationsleiste, um zwischen den Seiten zu wechseln:

1. Erstelle eine neue Datei `Navbar.jsx` im `components`-Ordner

2. Füge folgenden Code ein:

```jsx
// Link von 'next/link' importieren
import Link from "next/link";

export default function Navbar() {
    return (
        <nav className="bg-gray-100 p-4 shadow-sm">
            <div className="container mx-auto flex gap-4">
                {/* Links zu verschiedenen Seiten */}
                <Link href="/" className="font-medium hover:text-blue-600 transition-colors">
                    Startseite
                </Link>
                <Link href="/about" className="font-medium hover:text-blue-600 transition-colors">
                    Über uns
                </Link>
            </div>
        </nav>
    );
}
```

## Schritt 3: Navigation ins Layout einfügen

Jetzt fügen wir die Navigationsleiste in unser Hauptlayout ein:

1. Öffne die Datei `app/layout.js`

2. Importiere die Navbar-Komponente am Anfang der Datei:

```jsx
import Navbar from "@/components/Navbar";
```

> Hinweis: Das `@/` ist ein Alias, der auf das Root-Verzeichnis deines Projekts verweist.

3. Füge die Navbar-Komponente zwischen dem Header und dem Content-Bereich ein:

```jsx
export const metadata = {
    title: "News-Portal",
    description: "Ein einfaches News-Portal mit Next.js",
};

export default function RootLayout({ children }) {
    return (
        <html lang="de">
            <body className="bg-gray-50 min-h-screen">
                <header className="bg-blue-600 text-white p-4">
                    <div className="container mx-auto">
                        <h1 className="text-xl font-bold">News-Portal</h1>
                    </div>
                </header>

                {/* Navigationsleiste einfügen */}
                <Navbar />

                <div className="container mx-auto py-4">{children}</div>
                <footer className="bg-gray-200 p-4 mt-8">
                    <div className="container mx-auto text-center text-gray-600">
                        &copy; 2025 News-Portal - Next.js Seminar
                    </div>
                </footer>
            </body>
        </html>
    );
}
```

4. Speichere die Datei und prüfe im Browser, ob die Navigation erscheint und funktioniert

## Schritt 4: Aktive Link-Hervorhebung (Optional)

Lass uns die Navigation verbessern, indem wir den aktiven Link hervorheben:

1. Öffne die `Navbar.jsx`-Datei und ändere sie wie folgt:

```jsx
"use client";
import Link from "next/link";
import { usePathname } from "next/navigation";

export default function Navbar() {
    // Aktuellen Pfad abrufen
    const pathname = usePathname();

    // Funktion zum Bestimmen des Klassenstils basierend auf aktivem Link
    const getLinkClass = (path) => {
        const baseClass = "font-medium transition-colors";
        return pathname === path ? `${baseClass} text-blue-600 font-bold` : `${baseClass} hover:text-blue-600`;
    };

    return (
        <nav className="bg-gray-100 p-4 shadow-sm">
            <div className="container mx-auto flex gap-4">
                <Link href="/" className={getLinkClass("/")}>
                    Startseite
                </Link>
                <Link href="/about" className={getLinkClass("/about")}>
                    Über uns
                </Link>
            </div>
        </nav>
    );
}
```

> Hinweis: `'use client'` ist eine Direktive, die angibt, dass diese Komponente auf dem Client ausgeführt wird. Dies ist notwendig, um Hooks wie `usePathname` zu verwenden.

2. Speichere die Datei und teste die verbesserte Navigation

## Schritt 5: Eine weitere Seite hinzufügen

Jetzt füge selbstständig eine weitere Seite hinzu:

1. Erstelle einen Ordner `contact` im `app`-Verzeichnis
2. Erstelle eine `page.js`-Datei darin
3. Implementiere eine einfache Kontaktseite (füge Überschrift und Text hinzu)
4. Ergänze die Navigation um einen Link zur Kontaktseite

<details>
<summary>Hilfe zur Lösung anzeigen</summary>

```jsx
// app/contact/page.js
export default function Contact() {
    return (
        <main className="p-8">
            <h1 className="text-2xl font-bold mb-4">Kontakt</h1>
            <p className="mb-4">Du hast Fragen, Anregungen oder Feedback? Kontaktiere uns!</p>
            <p className="mb-4">Email: info@news-portal.example</p>
            <p>Telefon: +49 123 456789</p>
        </main>
    );
}
```

Und in der Navbar:

```jsx
<Link href="/contact" className={getLinkClass("/contact")}>
    Kontakt
</Link>
```

</details>

## Nächste Schritte

Großartig!
Du hast jetzt das grundlegende Routing von Next.js kennengelernt und mehrere Seiten mit einer Navigation erstellt. Im nächsten Teil werden wir uns mit Server- und Client-Komponenten befassen und interaktive Elemente hinzufügen.
