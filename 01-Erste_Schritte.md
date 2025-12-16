# Übung 1: Erste Schritte mit Next.js

## Ziel

In dieser Übung erstellst du dein erstes Next.js Projekt und machst dich mit der Projektstruktur vertraut. Du wirst die Startseite anpassen und erste Inhalte hinzufügen.

## Zeit: 30 Minuten

## Schritt 1: Projekt erstellen

1. Öffne ein Terminal und navigiere zum gewünschten Ordner
2. Führe folgenden Befehl aus, um ein neues Next.js Projekt zu erstellen:

```bash
npx create-next-app@latest news-portal
```

3. Beantworte die Fragen wie folgt:
    - Would you like to use TypeScript? → No (oder Yes, wenn du TypeScript kennst)
    - Would you like to use ESLint? → Yes
    - Would you like to use Tailwind CSS? → Yes
    - Would you like to use `src/` directory? → No
    - Would you like to use App Router? → Yes
    - Would you like to customize the default import alias? → No

4. Wechsle in das Projektverzeichnis:

```bash
cd news-portal
```

## Schritt 2: Projekt starten

1. Starte den Entwicklungsserver mit:

```bash
npm run dev
```

2. Öffne deinen Browser und gehe zu: [http://localhost:3000](http://localhost:3000)
3. Du solltest die Next.js Standardstartseite sehen.

## Schritt 3: Projektstruktur erkunden

Schaue dir die wichtigsten Dateien und Ordner an:

- `app/` - Hier liegen die Seiten und Routen deiner Anwendung
- `app/page.js` - Die Hauptseite deiner Anwendung
- `app/layout.js` - Das Hauptlayout, das auf alle Seiten angewendet wird
- `public/` - Statische Dateien wie Bilder, Fonts, etc.
- `next.config.js` - Next.js Konfigurationsdatei
- `package.json` - Projektabhängigkeiten und Skripte

## Schritt 4: Startseite anpassen

1. Öffne die Datei `app/page.js` in deinem Code-Editor
2. Lösche den gesamten vorhandenen Code
3. Füge den folgenden einfachen Code ein:

```jsx
export default function Home() {
    return (
        <main className="p-8">
            <h1 className="text-2xl font-bold">Mein News-Portal</h1>
            <p className="mt-4">Willkommen auf unserem Nachrichtenportal!</p>
        </main>
    );
}
```

4. Speichere die Datei und schau dir die Änderungen im Browser an

## Schritt 5: Layout anpassen

1. Öffne die Datei `app/layout.js`
2. Passe die Datei an, um einen ansprechenden Rahmen für alle Seiten zu erstellen:

```jsx
import "./globals.css";

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

## Schritt 6: Komponenten-Ordner erstellen

1. Erstelle einen neuen Ordner namens `components` im Hauptverzeichnis deines Projekts
2. Dieser Ordner wird später für wiederverwendbare Komponenten genutzt

## Schritt 7: Eigene Anpassungen (Optional)

1. Ändere den Titel und die Beschreibung in `metadata`
2. Passe die Farben im Layout an (z.B. ändere `bg-blue-600` zu einer anderen Farbe)
3. Füge einen weiteren Absatz zur Startseite hinzu

## Nächste Schritte

Glückwunsch! Du hast dein erstes Next.js-Projekt erstellt und angepasst. Im nächsten Teil werden wir uns mit dem Routing-System befassen und weitere Seiten hinzufügen.
