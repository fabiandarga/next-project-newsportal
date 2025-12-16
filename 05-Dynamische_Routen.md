# Übung 5: Dynamische Routen

## Ziel

In dieser Übung werden wir uns tiefer mit dynamischen Routen in Next.js befassen. Du wirst lernen, wie man Daten basierend auf URL-Parametern lädt und anzeigt.

## Zeit: 30 Minuten

## Schritt 1: Kategorieseiten erstellen

**Jetzt erstellen wir dynamische Routen für Nachrichtenkategorien:**

1. Stelle sicher, dass die Nachrichtendaten in `lib/data.js` jeweils eine `category` haben hinzu.
   Und füge eine Funktion `getNewsByCategory` hinzu.

```jsx
export const newsData = [
    {
        id: 1,
        title: "Next.js 14 veröffentlicht",
        // ... bestehende Daten
        category: "Framework",
    },
    // ...
];

// Hilfsfunktion zum Extrahieren von Kategorien
export function getCategories() {
    // Extrahiere alle Kategorien und entferne Duplikate
    return [...new Set(newsData.map((item) => item.category))];
}

// Hilfsfunktion zum Filtern nach Kategorie
export function getNewsByCategory(category) {
    return newsData.filter((item) => item.category === category);
}
```

2. Erstelle einen neuen Ordner `category` im `app`-Verzeichnis
3. Erstelle darin einen Ordner `[category]` (die eckigen Klammern zeigen an, dass es sich um eine dynamische Route handelt)
4. Erstelle eine neue Datei `page.js` im `[category]`-Ordner (_/app/category/[category]/page.tsx_)

```jsx
import { getNewsByCategory } from "@/lib/data";
import NewsCard from "@/components/NewsCard";
import Link from "next/link";

export default async function CategoryPage({ params }) {
    // Hole die Kategorie aus den URL-Parametern
    const { category } = await params;

    // Filtere Nachrichten nach Kategorie
    const filteredNews = getNewsByCategory(category);

    return (
        <main className="p-8">
            <div className="mb-6">
                <h1 className="text-2xl font-bold mb-2">Kategorie: {category}</h1>
                <Link href="/" className="text-blue-500 hover:underline">
                    ← Zurück zur Übersicht
                </Link>
            </div>

            {filteredNews.length === 0 ? (
                <p>Keine Nachrichten in dieser Kategorie gefunden.</p>
            ) : (
                <div className="grid gap-6">
                    {filteredNews.map((news) => (
                        <NewsCard
                            key={news.id}
                            id={news.id}
                            title={news.title}
                            excerpt={news.excerpt}
                            author={news.author}
                            date={news.date}
                            category={news.category}
                        />
                    ))}
                </div>
            )}
        </main>
    );
}
```

## Schritt 2: Kategorien auf der Startseite anzeigen

Jetzt aktualisieren wir die Startseite, um die Kategorien clickbar zu machen:

1. Öffne die Datei `app/page.js`
2. Importiere die `getCategories`-Funktion
3. Füge eine Kategorienliste hinzu:

```jsx
import NewsCard from "@/components/NewsCard";
import ThemeButton from "@/components/ThemeButton";
import { newsData, getCategories } from "@/lib/data";
import Link from "next/link";

export default function Home() {
    // Kategorien abrufen
    const categories = getCategories();

    return (
        <main className="p-8">
            <div className="flex justify-between items-center mb-8">
                <h1 className="text-2xl font-bold">Aktuelle Nachrichten</h1>
                <ThemeButton />
            </div>

            {/* Kategorienliste */}
            <div className="mb-6">
                <h2 className="text-lg font-semibold mb-2">Kategorien:</h2>
                <div className="flex gap-2 flex-wrap">
                    {/* Clickbare Category-Tags */}
                    {categories.map((category) => (
                        <Link
                            key={category}
                            href={`/category/${category}`}
                            className="px-3 py-1 bg-gray-200 rounded-full text-sm hover:bg-gray-300 transition-colors"
                        >
                            {category}
                        </Link>
                    ))}
                </div>
            </div>

            {/* Nachrichtenliste */}
            <div className="grid gap-6">
                {newsData.map((news) => (
                    <NewsCard
                        key={news.id}
                        id={news.id}
                        title={news.title}
                        excerpt={news.excerpt}
                        author={news.author}
                        date={news.date}
                        category={news.category}
                    />
                ))}
            </div>
        </main>
    );
}
```

## Schritt 3: NewsCard um Kategorien erweitern

Jetzt aktualisieren wir die NewsCard-Komponente, um die Kategorie anzuzeigen:

Ein Link zur Kategorien sollte als eigene Komponente implementiert werden:
_/components/CategoryLink.tsx_

```tsx
"use client";
import Link from "next/link";

export default function CategoryLink({ category }) {
    const router = useRouter();

    const handleClick = (e) => {
        e.stopPropagation();
        e.preventDefault();
        router.push(`/category/${category}`);
    };

    return (
        <div
            href={`/category/${category}`}
            className="inline-block mt-2 px-2 py-1 text-xs bg-blue-100 text-blue-800 rounded-full"
            onClick={handleClick}
        >
            {category}
        </div>
    );
}
```

Das verhinder ein verschachteltes <a> in einem <a>, was in HTML (und react) verboten ist.
Ebenso kann der Link ein 'use client' bekommen und die NewsCard bleibt eine Server-Component.

1. Öffne die Datei `components/NewsCard.jsx`
2. Ersetze die Kategorie mit dem CategoryLink:

```jsx
import LikeButton from "./LikeButton";
import CategoryLink from "@/components/CategoryLink";

export default function NewsCard({ id, title, excerpt, author, date, category }) {
    const formattedDate = new Date(date).toLocaleDateString("de-DE", {
        year: "numeric",
        month: "long",
        day: "numeric",
    });

    return (
        <Link href={`/news/${id}`} className="block">
            <article className="border p-4 rounded-lg shadow-sm bg-white hover:shadow-md transition-shadow">
                <h2 className="text-xl font-bold text-gray-800">{title}</h2>

                {/* Kategorie-Link hinzufügen */}
                {category && <CategoryLink category={category} />}

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
        </Link>
    );
}
```

## Schritt 4: News-Detailseite aktualisieren

Jetzt aktualisieren wir die Detailseite, um auch die Kategorie anzuzeigen:

1. Öffne die Datei `app/news/[id]/page.js`
2. Füge die Kategorie zur Anzeige hinzu:

```jsx
import { newsData } from "@/lib/data";
import Link from "next/link";

export default async function NewsDetail({ params }) {
    const { id } = await params;
    const newsItem = newsData.find((item) => item.id === parseInt(params.id));

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

    const formattedDate = new Date(newsItem.date).toLocaleDateString("de-DE", {
        year: "numeric",
        month: "long",
        day: "numeric",
    });

    return (
        <article className="p-8 max-w-3xl mx-auto">
            <h1 className="text-3xl font-bold mb-4">{newsItem.title}</h1>

            {/* Kategorie anzeigen */}
            <CategoryLink category={newsItem.category} />

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

## Schritt 5: Layout für Kategorieseiten (Aufgabe)

Erstelle ein spezielles Layout nur für die Kategorie-Seiten:

1. Erstelle eine neue Datei `layout.js` im Ordner `app/category/[category]/`
2. Implementiere ein einfaches Layout, das den Kategorienamen in einem farbigen Banner anzeigt

<details>
<summary>Hilfe zur Lösung anzeigen</summary>

```jsx
// app/category/[category]/layout.js
export default async function CategoryLayout({ children, params }) {
    const { category } = await params;
    return (
        <div>
            <div className="bg-blue-600 text-white p-4 mb-4">
                <div className="container mx-auto">
                    <h1 className="text-xl font-bold">Kategorie: {params.category}</h1>
                </div>
            </div>
            <div className="container mx-auto">{children}</div>
        </div>
    );
}
```

Und entsprechend die `page.js` anpassen:

```jsx
// app/category/[category]/page.js
import { getNewsByCategory } from "@/lib/data";
import NewsCard from "@/components/NewsCard";
import Link from "next/link";

export default async function CategoryPage({ params }) {
    const { category } = await params;
    const filteredNews = getNewsByCategory(category);

    return (
        <main className="p-8">
            <div className="mb-6">
                <Link href="/" className="text-blue-500 hover:underline">
                    ← Zurück zur Übersicht
                </Link>
            </div>

            {filteredNews.length === 0 ? (
                <p>Keine Nachrichten in dieser Kategorie gefunden.</p>
            ) : (
                <div className="grid gap-6">
                    {filteredNews.map((news) => (
                        <NewsCard
                            key={news.id}
                            id={news.id}
                            title={news.title}
                            excerpt={news.excerpt}
                            author={news.author}
                            date={news.date}
                            category={news.category}
                        />
                    ))}
                </div>
            )}
        </main>
    );
}
```

</details>

## Nächste Schritte

Super! Du hast jetzt gelernt, wie dynamische Routen in Next.js funktionieren und wie du sie für Kategorien und Detailseiten einsetzen kannst. Im nächsten Teil werden wir uns mit dem Abrufen von Daten aus einer externen API befassen.
