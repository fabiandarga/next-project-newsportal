# Übung 3: Server & Client Components

## Ziel

In dieser Übung lernst du den Unterschied zwischen Server und Client Components in Next.js kennen. Du wirst beide Arten von Komponenten erstellen und sie auf deiner Startseite verwenden.

## Zeit: 30 Minuten

## Was sind Server und Client Components?

- **Server Components**: Werden auf dem Server gerendert und zum Client gesendet. Sie können keine Browser-APIs oder React-Hooks verwenden.
- **Client Components**: Werden auf dem Client (Browser) gerendert und können interaktive Elemente wie Hooks, Event Handler und Browser-APIs verwenden.
- **Wichtig**: In Next.js 14 sind alle Komponenten standardmäßig Server Components, es sei denn, sie beginnen mit der Direktive `'use client'`.

## Schritt 1: Eine Server Component erstellen

Zuerst erstellen wir eine einfache Server Component für die Anzeige von Nachrichtenkarten:

1. Erstelle eine neue Datei `NewsCard.jsx` im `components`-Ordner
2. Füge folgenden Code ein:

```jsx
export default function NewsCard({ title, excerpt }) {
    // Diese Komponente wird auf dem Server gerendert
    return (
        <div className="border p-4 rounded-lg shadow-sm bg-white hover:shadow-md transition-shadow">
            <h2 className="text-xl font-bold text-gray-800">{title}</h2>
            <p className="mt-3 text-gray-600">{excerpt}</p>
        </div>
    );
}
```

Diese Komponente wird auf dem Server gerendert und als HTML an den Client gesendet.

## Schritt 2: Eine Client Component erstellen

Jetzt erstellen wir eine interaktive Client Component mit State:

1. Erstelle eine neue Datei `ThemeButton.jsx` im `components`-Ordner
2. Füge folgenden Code ein:

```jsx
"use client";
// Wichtig: 'use client' muss in der ersten Zeile stehen!

// React importieren und den useState Hook
import { useState } from "react";

export default function ThemeButton() {
    // useState kann nur in Client Components verwendet werden
    const [isDark, setIsDark] = useState(false);

    // Event Handler für Klick-Events
    const toggleTheme = () => {
        setIsDark(!isDark);
        // In einer echten App würden wir hier auch die Klasse im body-Element ändern
        console.log(`Theme geändert zu: ${isDark ? "Hell" : "Dunkel"}`);
    };

    return (
        <button
            onClick={toggleTheme}
            className={`px-4 py-2 rounded-md transition-colors ${
                isDark ? "bg-gray-800 text-white" : "bg-blue-500 text-white"
            }`}
        >
            {isDark ? "Hell" : "Dunkel"}
        </button>
    );
}
```

Diese Komponente wird auf dem Client gerendert und kann interaktiv sein (State ändern, Events behandeln).

## Schritt 3: Komponenten auf der Startseite verwenden

Jetzt verwenden wir unsere neuen Komponenten auf der Startseite:

1. Öffne die Datei `app/page.js`
2. Importiere die erstellten Komponenten
3. Füge einige Beispielnachrichten hinzu

```jsx
// Server Components importieren
import NewsCard from "@/components/NewsCard";
import ThemeButton from "@/components/ThemeButton";

// Beispieldaten für Nachrichten
const sampleNews = [
    {
        id: 1,
        title: "Next.js 14 veröffentlicht",
        excerpt: "Die neueste Version bringt viele Verbesserungen für Server Components und Rendering-Optimierungen.",
    },
    {
        id: 2,
        title: "React Server Components im Einsatz",
        excerpt: "Wie die neuen Komponenten die Entwicklung moderner Webanwendungen grundlegend verändern.",
    },
    {
        id: 3,
        title: "Tailwind CSS wird immer beliebter",
        excerpt: "Das Utility-First CSS-Framework setzt sich in immer mehr Projekten durch.",
    },
];

export default function Home() {
    return (
        <main className="p-8">
            <div className="flex justify-between items-center mb-8">
                <h1 className="text-2xl font-bold">Aktuelle Nachrichten</h1>
                <ThemeButton />
            </div>

            <div className="grid gap-6">
                {sampleNews.map((news) => (
                    <NewsCard key={news.id} title={news.title} excerpt={news.excerpt} />
                ))}
            </div>
        </main>
    );
}
```

4. Speichere die Datei und prüfe im Browser, ob die Nachrichtenkarten angezeigt werden und der Theme-Button funktioniert

## Schritt 4: Eine weitere Client Component erstellen

Lass uns eine weitere Client Component für ein einfaches Like-System erstellen:

1. Erstelle eine neue Datei `LikeButton.jsx` im `components`-Ordner

2. Füge folgenden Code ein:

```jsx
"use client";
import { useState } from "react";

export default function LikeButton() {
    const [likes, setLikes] = useState(0);
    const [liked, setLiked] = useState(false);

    const handleLike = () => {
        if (!liked) {
            setLikes(likes + 1);
            setLiked(true);
        } else {
            setLikes(likes - 1);
            setLiked(false);
        }
    };

    return (
        <button
            onClick={handleLike}
            className={`flex items-center gap-2 px-3 py-1 rounded-md ${liked ? "text-red-500" : "text-gray-500"}`}
        >
            <span>{liked ? "❤️" : "🤍"}</span>
            <span>{likes}</span>
        </button>
    );
}
```

## Schritt 5: NewsCard erweitern (Aufgabe)

Jetzt erweitere die `NewsCard`-Komponente, um den LikeButton einzubinden:

1. Öffne die `NewsCard.jsx` Datei
2. Importiere die LikeButton-Komponente
3. Füge sie am Ende der Karte ein

<details>
<summary>Hilfe zur Lösung anzeigen</summary>

```jsx
import LikeButton from "./LikeButton";

export default function NewsCard({ title, excerpt }) {
    return (
        <div className="border p-4 rounded-lg shadow-sm bg-white hover:shadow-md transition-shadow">
            <h2 className="text-xl font-bold text-gray-800">{title}</h2>
            <p className="mt-3 text-gray-600">{excerpt}</p>
            <div className="mt-4 flex justify-end">
                <LikeButton />
            </div>
        </div>
    );
}
```

</details>

## Schritt 6: Verstehe Client/Server Komponenten-Zusammenspiel

Lass uns eine weitere Client Component erstellen, die eine serverseitige Eigenschaft wie aktuelle Zeit anzeigt:

1. Erstelle eine neue Datei `TimeDisplay.jsx` im `components`-Ordner:

```jsx
// Diese Komponente bleibt eine Server Component
export default function TimeDisplay() {
    // Dies wird auf dem Server ausgeführt
    const currentTime = new Date().toLocaleTimeString("de-DE");

    return <div className="text-sm text-gray-500 mt-2">Aktuelle Serverzeit: {currentTime}</div>;
}
```

2. Füge diese Komponente zur NewsCard hinzu, um zu sehen, dass die Zeit nur beim Server-Rendering aktualisiert wird, nicht bei Client-Interaktionen:

```jsx
import LikeButton from "./LikeButton";
import TimeDisplay from "./TimeDisplay";

export default function NewsCard({ title, excerpt }) {
    return (
        <div className="border p-4 rounded-lg shadow-sm bg-white hover:shadow-md transition-shadow">
            <h2 className="text-xl font-bold text-gray-800">{title}</h2>
            <p className="mt-3 text-gray-600">{excerpt}</p>
            <TimeDisplay />
            <div className="mt-4 flex justify-end">
                <LikeButton />
            </div>
        </div>
    );
}
```

3. Speichere die Dateien und beobachte, dass die Zeit statisch bleibt, auch wenn du auf den Like-Button klickst.

## Nächste Schritte

Großartig!
Du hast jetzt den Unterschied zwischen Server und Client Components in Next.js verstanden und gelernt, wie du sie kombinieren kannst. Im nächsten Teil werden wir uns mit dem Datenabruf in Next.js befassen.
