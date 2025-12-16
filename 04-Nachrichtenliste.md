# Übung 4: Nachrichtenliste anzeigen

## Ziel

In dieser Übung lernst du, wie du statische Daten in Next.js strukturierst und anzeigst. Wir werden eine Nachrichtenliste mit Dummy-Daten erstellen und diese anzeigen.

## Zeit: 30 Minuten

## Schritt 1: Datenstruktur erstellen

Zuerst erstellen wir einen Ort für unsere statischen Daten:

1. Erstelle einen neuen Ordner `lib` im Hauptverzeichnis deines Projekts
2. Erstelle darin eine neue Datei `data.js`
3. Füge folgende Nachrichtendaten ein:

```jsx
export const newsData = [
    {
        id: 1,
        title: "Next.js 14 veröffentlicht",
        excerpt: "Die neueste Version bringt viele Verbesserungen für Server Components und Rendering-Optimierungen.",
        content:
            "Next.js 14 wurde als große Weiterentwicklung des beliebten React-Frameworks veröffentlicht. Die neue Version fokussiert sich auf Server Components, verbesserte Build-Zeiten und kleinere JavaScript-Bundles. Der App Router wurde weiter optimiert, um eine noch bessere Entwicklererfahrung zu bieten. Vercel, das Unternehmen hinter Next.js, berichtet von bis zu 90% schnelleren Build-Zeiten bei großen Anwendungen.",
        author: "Max Mustermann",
        date: "2025-10-15",
    },
    {
        id: 2,
        title: "React Server Components im Einsatz",
        excerpt: "Wie die neuen Komponenten die Entwicklung moderner Webanwendungen grundlegend verändern.",
        content:
            "React Server Components (RSC) stellen einen Paradigmenwechsel in der Frontend-Entwicklung dar. Anders als klassische React-Komponenten werden sie vollständig auf dem Server gerendert und reduzieren damit die Menge an JavaScript, die zum Browser gesendet werden muss. Dies führt zu schnelleren Ladezeiten und einer besseren Performance. Entwickler berichten von einer bis zu 30% Reduktion der Bundle-Größen und einer verbesserten User Experience, besonders auf mobilen Geräten mit langsamer Internetverbindung.",
        author: "Anna Schmidt",
        date: "2025-10-12",
    },
    {
        id: 3,
        title: "Tailwind CSS wird immer beliebter",
        excerpt: "Das Utility-First CSS-Framework setzt sich in immer mehr Projekten durch.",
        content:
            'Tailwind CSS hat sich in den letzten Jahren als eines der beliebtesten CSS-Frameworks etabliert. Der "Utility-First"-Ansatz, bei dem Klassen direkt im HTML verwendet werden, hat viele Anhänger gefunden. Laut einer aktuellen Umfrage unter Frontend-Entwicklern nutzen mittlerweile über 45% Tailwind CSS für neue Projekte. Besonders in Kombination mit modernen JavaScript-Frameworks wie React und Next.js erfreut sich Tailwind großer Beliebtheit. Die jüngsten Verbesserungen in Version 4.0 haben das Framework noch leistungsfähiger gemacht.',
        author: "Tom Weber",
        date: "2025-10-08",
    },
    {
        id: 4,
        title: "TypeScript dominiert JavaScript-Ökosystem",
        excerpt: "Immer mehr Entwickler setzen auf statische Typisierung mit TypeScript.",
        content:
            "TypeScript hat sich endgültig als Standard in der JavaScript-Entwicklung etabliert. Laut den neuesten Statistiken von GitHub wird TypeScript in über 60% aller neuen JavaScript-Projekte verwendet. Die Vorteile der statischen Typisierung, bessere Entwicklertools und frühzeitige Fehlererkennung überzeugen immer mehr Teams. Auch große Frameworks wie Angular, Next.js und Vue empfehlen TypeScript als bevorzugte Sprache. Microsoft, der Entwickler hinter TypeScript, hat mit Version 5.2 weitere Verbesserungen eingeführt, die die Entwicklererfahrung noch weiter verbessern.",
        author: "Lisa Müller",
        date: "2025-10-05",
    },
    {
        id: 5,
        title: "KI-Tools revolutionieren Webentwicklung",
        excerpt: "Neue KI-gestützte Tools helfen Entwicklern, schneller und effizienter zu arbeiten.",
        content:
            "Künstliche Intelligenz verändert die Art und Weise, wie Webentwickler arbeiten. Tools wie GitHub Copilot, Claude Code und andere KI-Assistenten übernehmen zunehmend repetitive Aufgaben und generieren Code-Vorschläge. Eine aktuelle Studie zeigt, dass Entwickler mit KI-Unterstützung bis zu 40% produktiver sind und weniger Zeit mit Debugging verbringen. Besonders bei der Erstellung von Standardkomponenten und der Implementierung bekannter Patterns können diese Tools viel Zeit sparen. Experten prognostizieren, dass bis 2027 über 80% der Webentwickler regelmäßig KI-Tools in ihrem Arbeitsablauf einsetzen werden.",
        author: "David Fischer",
        date: "2025-10-01",
    },
];
```

## Schritt 2: NewsCard Komponente anpassen

Jetzt passen wir die NewsCard-Komponente an, um mehr Details anzuzeigen:

1. Öffne die Datei `components/NewsCard.jsx`
2. Ändere den Code wie folgt:

```jsx
import LikeButton from "./LikeButton";

// Wir erweitern die Props, um weitere Daten anzuzeigen
export default function NewsCard({ title, excerpt, author, date }) {
    // Formatiere das Datum als lesbare Zeichenkette
    const formattedDate = new Date(date).toLocaleDateString("de-DE", {
        year: "numeric",
        month: "long",
        day: "numeric",
    });

    return (
        <article className="border p-4 rounded-lg shadow-sm bg-white hover:shadow-md transition-shadow">
            <h2 className="text-xl font-bold text-gray-800">{title}</h2>
            <p className="mt-3 text-gray-600">{excerpt}</p>

            <div className="mt-4 flex justify-between items-center">
                <div className="text-sm text-gray-500">
                    <span>{author}</span>
                    <span className="mx-1">•</span>
                    <span>{formattedDate}</span>
                </div>
                <LikeButton />
            </div>
        </article>
    );
}
```

## Schritt 3: Startseite mit Nachrichtendaten aktualisieren

Jetzt aktualisieren wir die Startseite, um unsere strukturierten Daten anzuzeigen:

1. Öffne die Datei `app/page.js`
2. Importiere die Nachrichtendaten aus der data.js-Datei
3. Ersetze den Inhalt mit folgendem Code:

```jsx
// Importiere Komponenten und Daten
import NewsCard from "@/components/NewsCard";
import ThemeButton from "@/components/ThemeButton";
import { newsData } from "@/lib/data";

export default function Home() {
    return (
        <main className="p-8">
            <div className="flex justify-between items-center mb-8">
                <h1 className="text-2xl font-bold">Aktuelle Nachrichten</h1>
                <ThemeButton />
            </div>

            <div className="grid gap-6">
                {newsData.map((news) => (
                    <NewsCard
                        key={news.id}
                        title={news.title}
                        excerpt={news.excerpt}
                        author={news.author}
                        date={news.date}
                    />
                ))}
            </div>
        </main>
    );
}
```

4. Speichere die Datei und überprüfe im Browser, ob die Nachrichtenliste mit den Autor- und Datumsangaben angezeigt wird

## Schritt 4: Detailseite für Nachrichten erstellen

Jetzt erstellen wir eine Detailseite, die den vollständigen Inhalt einer Nachricht anzeigt:

1. Erstelle einen neuen Ordner `news` im `app`-Verzeichnis
2. Erstelle darin einen weiteren Ordner `[id]` (die eckigen Klammern sind wichtig für dynamische Routen!)
3. Erstelle eine neue Datei `page.js` im `[id]`-Ordner
4. Füge folgenden Code ein:

```jsx
import { newsData } from "@/lib/data";
import Link from "next/link";

// Diese Funktion wird auf dem Server ausgeführt
export default async function NewsDetail({ params }) {
    const { id } = await params; // params sind ein Promise

    // Finde die Nachricht mit der passenden ID
    const newsItem = newsData.find((item) => item.id === parseInt(id));

    // Falls keine Nachricht gefunden wurde
    if (!newsItem) {
        return (
            <div className="p-8">
                <h1 className="text-2xl font-bold mb-4">Artikel nicht gefunden</h1>
                <Link href="/" className="text-blue-500 hover:underline">
                    ← Zurück zur Übersicht
                </Link>
            </div>
        );
    }

    // Formatiere das Datum
    const formattedDate = new Date(newsItem.date).toLocaleDateString("de-DE", {
        year: "numeric",
        month: "long",
        day: "numeric",
    });

    return (
        <article className="p-8 max-w-3xl mx-auto">
            <h1 className="text-3xl font-bold mb-4">{newsItem.title}</h1>

            <div className="mb-6 text-gray-500">
                <span>Von {newsItem.author}</span>
                <span className="mx-1">•</span>
                <span>{formattedDate}</span>
            </div>

            <p className="text-gray-700 mb-6 leading-relaxed">{newsItem.content}</p>

            <Link href="/" className="text-blue-500 hover:underline">
                ← Zurück zur Übersicht
            </Link>
        </article>
    );
}
```

## Schritt 5: NewsCard zu einem Link machen

Jetzt müssen wir die NewsCard-Komponente so anpassen, dass sie zur Detailseite verlinkt:

1. Öffne die Datei `components/NewsCard.jsx`
2. Passe den Code wie folgt an:

```jsx
import Link from "next/link";
import LikeButton from "./LikeButton";

// Wir fügen id als weitere Prop hinzu
export default function NewsCard({ id, title, excerpt, author, date }) {
    const formattedDate = new Date(date).toLocaleDateString("de-DE", {
        year: "numeric",
        month: "long",
        day: "numeric",
    });

    return (
        <Link href={`/news/${id}`} className="block">
            <article className="border p-4 rounded-lg shadow-sm bg-white hover:shadow-md transition-shadow">
                <h2 className="text-xl font-bold text-gray-800">{title}</h2>
                <p className="mt-3 text-gray-600">{excerpt}</p>

                <div className="mt-4 flex justify-between items-center">
                    <div className="text-sm text-gray-500">
                        <span>{author}</span>
                        <span className="mx-1">•</span>
                        <span>{formattedDate}</span>
                    </div>

                    {/* Wir müssen den Like-Button mit einem Div umschließen,
              damit der Klick auf den Button nicht zur Detailseite führt */}
                    <div onClick={(e) => e.stopPropagation()}>
                        <LikeButton />
                    </div>
                </div>
            </article>
        </Link>
    );
}
```

Damit ein Click auf den Like-Button nicht zusätzlich eine Navigation auslöst, muss ein `event.preventDefault()` eingesetzt werden.
Im LikeButton:

```jsx
const handleLike = (event: MouseEvent) => {
    event.stopPropagation() // < verhindert Event bubbling
    event.preventDefault()  // < verhinder Browser Standard

    // ...
};
```

3. Aktualisiere auch die Startseite, um die ID an die NewsCard-Komponente zu übergeben:

```jsx
// In app/page.js
<div className="grid gap-6">
    {newsData.map((news) => (
        <NewsCard
            key={news.id}
            id={news.id} // Füge id hinzu
            title={news.title}
            excerpt={news.excerpt}
            author={news.author}
            date={news.date}
        />
    ))}
</div>
```

## Schritt 6: Eine Kategorienliste hinzufügen (Aufgabe)

Ergänze die Datenstruktur und die Anzeige um Kategorien:

1. Füge zu jeder Nachricht in `data.js` eine Kategorie hinzu (z.B. "Technologie", "Web-Entwicklung", usw.)
2. Erstelle eine einfache Liste von Kategorien in der Seitenleiste oder am Anfang der Startseite

<details>
<summary>Hilfe zur Lösung anzeigen</summary>

Zuerst in `lib/data.js` die Kategorie hinzufügen:

```jsx
export const newsData = [
    {
        id: 1,
        title: "Next.js 14 veröffentlicht",
        excerpt: "...",
        content: "...",
        author: "Max Mustermann",
        date: "2025-10-15",
        category: "Framework",
    },
    // ... andere Einträge mit Kategorien ergänzen
];

// Kategorien extrahieren (Hilfsfunktion)
export function getCategories() {
    // Sammle alle Kategorien und entferne Duplikate
    const categories = [...new Set(newsData.map((item) => item.category))];
    return categories;
}
```

Dann in `app/page.js` die Kategorien anzeigen:

```jsx
import { newsData, getCategories } from "@/lib/data";

export default function Home() {
    const categories = getCategories();

    return (
        <main className="p-8">
            {/* ... */}

            <div className="mb-6">
                <h2 className="text-lg font-semibold mb-2">Kategorien:</h2>
                <div className="flex gap-2 flex-wrap">
                    {categories.map((category) => (
                        <span key={category} className="px-3 py-1 bg-gray-200 rounded-full text-sm">
                            {category}
                        </span>
                    ))}
                </div>
            </div>

            <div className="grid gap-6">{/* ... Nachrichtenkarten ... */}</div>
        </main>
    );
}
```

</details>

## Nächste Schritte

Hervorragend! Du hast jetzt ein funktionierendes News-Portal mit statischen Daten, einer Nachrichtenliste und Detailseiten erstellt. Im nächsten Teil werden wir uns mit dynamischem Datenabruf von einer API befassen, um echte Nachrichtendaten anzuzeigen.
