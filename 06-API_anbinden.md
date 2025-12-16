# Übung 6: Daten von einer API laden

## Ziel

In dieser Übung lernst du, wie du Daten von einer externen API abrufen kannst. Wir ersetzen die statischen Daten in unserem News-Portal durch Daten von einer öffentlichen API.

## Zeit: 45 Minuten

## Schritt 1: Data Fetching verstehen

In Next.js gibt es verschiedene Möglichkeiten, Daten abzurufen:

- **Server Components**: Async/Await direkt in Komponenten
- **Route Handlers**: API-Endpunkte in Next.js
- **Client Components**: Daten auf Clientseite laden (useEffect, SWR, React Query)

In dieser Übung verwenden wir **Server Components** für den Datenabruf, was in Next.js 14 der empfohlene Ansatz ist.

## Schritt 2: API-Hilfs-Datei erstellen

Zuerst erstellen wir eine Hilfsdatei, die alle API-bezogenen Funktionen enthält:

1. Erstelle eine neue Datei `api.js` im `lib`-Ordner
2. Füge folgenden Code ein:

```jsx
// Wir verwenden die kostenlose JSONPlaceholder API für Testdaten
// https://jsonplaceholder.typicode.com

// Basis-URL der API
const API_BASE_URL = "https://my-json-server.typicode.com/fabiandarga/next-project-newsportal";

// Funktion zum Abrufen aller Beiträge (limitiert auf 10)
export async function fetchArticles() {
    try {
        const response = await fetch(`${API_BASE_URL}/data?_limit=10`);

        if (!response.ok) {
            throw new Error("Fehler beim Laden der Beiträge");
        }

        const articles = await response.json();

        return articles;
    } catch (error) {
        console.error("API Fehler:", error);
        return [];
    }
}

// Funktion zum Abrufen eines einzelnen Beitrags nach ID
export async function fetchArticle(id) {
    try {
        const response = await fetch(`${API_BASE_URL}/data/${id}`);

        if (!response.ok) {
            throw new Error("Beitrag nicht gefunden");
        }

        const article = await response.json();

        return article;
    } catch (error) {
        console.error("API Fehler:", error);
        return null;
    }
}

// Funktion zum Abrufen von Beiträgen nach Kategorie
export async function fetchArticlesByCategory(category) {
    const allArticles = await fetchArticles();
    return allArticles.filter((article) => article.category === category);
}

// Funktion zum Abrufen aller verfügbaren Kategorien
export async function fetchCategories() {
    const articles = await fetchArticles();
    const categories = [...new Set(articles.map((article) => article.category))];
    return categories;
}
```

## Schritt 3: Startseite mit API-Daten aktualisieren

Jetzt aktualisieren wir die Startseite, um Daten von der API anzuzeigen:

1. Öffne die Datei `app/page.js`
2. Ersetze den Inhalt mit folgendem Code:

```jsx
import NewsCard from "@/components/NewsCard";
import ThemeButton from "@/components/ThemeButton";
import Link from "next/link";
import { fetchArticles, fetchCategories } from "@/lib/api";

// Diese Komponente wird auf dem Server ausgeführt
export default async function Home() {
    // Daten von der API abrufen
    const articles = await fetchArticles();
    const categories = await fetchCategories();

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
                {articles.map((article) => (
                    <NewsCard
                        key={article.id}
                        id={article.id}
                        title={article.title}
                        excerpt={article.excerpt}
                        author={article.author}
                        date={article.date}
                        category={article.category}
                    />
                ))}
            </div>
        </main>
    );
}
```

## Schritt 4: Lade- und Fehlerstatus hinzufügen

In Next.js 14 können wir Lade- und Fehlerzeiten mit speziellen Dateien behandeln:

1. Erstelle eine Datei `loading.js` im `app`-Verzeichnis:

```jsx
export default function Loading() {
    return (
        <div className="p-8">
            <div className="flex justify-between items-center mb-8">
                <div className="h-8 w-64 bg-gray-200 rounded animate-pulse"></div>
                <div className="h-10 w-24 bg-gray-200 rounded animate-pulse"></div>
            </div>

            <div className="mb-6">
                <div className="h-6 w-40 bg-gray-200 rounded animate-pulse mb-2"></div>
                <div className="flex gap-2 flex-wrap">
                    {[1, 2, 3, 4].map((i) => (
                        <div key={i} className="h-8 w-20 bg-gray-200 rounded-full animate-pulse"></div>
                    ))}
                </div>
            </div>

            <div className="grid gap-6">
                {[1, 2, 3, 4, 5].map((i) => (
                    <div key={i} className="border p-4 rounded-lg">
                        <div className="h-7 w-3/4 bg-gray-200 rounded animate-pulse mb-4"></div>
                        <div className="h-4 bg-gray-200 rounded animate-pulse mb-2"></div>
                        <div className="h-4 bg-gray-200 rounded animate-pulse mb-2"></div>
                        <div className="h-4 w-1/2 bg-gray-200 rounded animate-pulse"></div>
                        <div className="mt-4 flex justify-between items-center">
                            <div className="h-4 w-32 bg-gray-200 rounded animate-pulse"></div>
                            <div className="h-8 w-8 bg-gray-200 rounded animate-pulse"></div>
                        </div>
                    </div>
                ))}
            </div>
        </div>
    );
}
```

2. Erstelle eine Datei `error.js` im `app`-Verzeichnis:

```jsx
"use client"; // Fehlerboundaries müssen Client-Komponenten sein

import { useEffect } from "react";

export default function Error({ error, reset }) {
    useEffect(() => {
        // Log the error to an error reporting service
        console.error("Fehler aufgetreten:", error);
    }, [error]);

    return (
        <div className="p-8 text-center">
            <h1 className="text-2xl font-bold mb-4 text-red-600">Etwas ist schief gelaufen!</h1>
            <p className="mb-6">{error.message || "Es ist ein unerwarteter Fehler aufgetreten."}</p>
            <button
                className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 transition-colors"
                onClick={() => reset()} // Versucht, die Komponente neu zu rendern
            >
                Erneut versuchen
            </button>
        </div>
    );
}
```

## Schritt 5: Detailseite aktualisieren

Jetzt aktualisieren wir die Detailseite, um Daten von der API abzurufen:

1. Öffne die Datei `app/news/[id]/page.js`
2. Ersetze den Inhalt mit folgendem Code:

```jsx
import { fetchArticle } from "@/lib/api";
import Link from "next/link";

export default async function NewsDetail({ params }) {
    const article = await fetchArticle(params.id);

    if (!article) {
        return (
            <div className="p-8">
                <h1 className="text-2xl font-bold mb-4">Artikel nicht gefunden</h1>
                <Link href="/" className="text-blue-500 hover:underline">
                    ← Zurück zur Übersicht
                </Link>
            </div>
        );
    }

    const formattedDate = new Date(article.date).toLocaleDateString("de-DE", {
        year: "numeric",
        month: "long",
        day: "numeric",
    });

    return (
        <article className="p-8 max-w-3xl mx-auto">
            <h1 className="text-3xl font-bold mb-4">{article.title}</h1>

            <CategoryLink category={article.category} />

            <div className="mb-6 text-gray-500">
                <span>Von {article.author}</span>
                <span className="mx-1">•</span>
                <span>{formattedDate}</span>
            </div>

            <p className="text-gray-700 mb-6 leading-relaxed">{article.content}</p>

            <Link href="/" className="text-blue-500 hover:underline">
                ← Zurück zur Übersicht
            </Link>
        </article>
    );
}
```

## Schritt 6: Kategorieseite aktualisieren

Jetzt aktualisieren wir auch die Kategorieseite:

1. Öffne die Datei `app/category/[category]/page.js`
2. Ersetze den Inhalt mit folgendem Code:

```jsx
import { fetchArticlesByCategory } from "@/lib/api";
import NewsCard from "@/components/NewsCard";
import Link from "next/link";

export default async function CategoryPage({ params }) {
    // Hole die Kategorie aus den URL-Parametern
    const { category } = params;

    // Filtere Nachrichten nach Kategorie
    const filteredArticles = await fetchArticlesByCategory(category);

    return (
        <main className="p-8">
            <div className="mb-6">
                <h1 className="text-2xl font-bold mb-2">Kategorie: {category}</h1>
                <Link href="/" className="text-blue-500 hover:underline">
                    ← Zurück zur Übersicht
                </Link>
            </div>

            {filteredArticles.length === 0 ? (
                <p>Keine Artikel in dieser Kategorie gefunden.</p>
            ) : (
                <div className="grid gap-6">
                    {filteredArticles.map((article) => (
                        <NewsCard
                            key={article.id}
                            id={article.id}
                            title={article.title}
                            excerpt={article.excerpt}
                            author={article.author}
                            date={article.date}
                            category={article.category}
                        />
                    ))}
                </div>
            )}
        </main>
    );
}
```

## Schritt 7: Suchmöglichkeit hinzufügen (Aufgabe)

Erstelle eine einfache Suchfunktion auf der Clientseite:

1. Erstelle eine neue Datei `SearchBox.jsx` im `components`-Ordner
2. Implementiere eine Client-Komponente mit einem Suchfeld
3. Integriere die Suche auf der Startseite

<details>
<summary>Hilfe zur Lösung anzeigen</summary>

`components/SearchBox.jsx`:

```jsx
"use client";
import { useState } from "react";

export default function SearchBox({ onSearch }) {
    const [query, setQuery] = useState("");

    const handleSubmit = (e) => {
        e.preventDefault();
        onSearch(query);
    };

    return (
        <form onSubmit={handleSubmit} className="mb-6">
            <div className="flex gap-2">
                <input
                    type="text"
                    value={query}
                    onChange={(e) => setQuery(e.target.value)}
                    placeholder="Nachrichten durchsuchen..."
                    className="flex-grow px-4 py-2 border rounded-lg"
                />
                <button
                    type="submit"
                    className="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors"
                >
                    Suchen
                </button>
            </div>
        </form>
    );
}
```

Dann in `app/page.js` die Startseite zu einer Client-Komponente machen:

```jsx
"use client";

import { useEffect, useState } from "react";
import NewsCard from "@/components/NewsCard";
import ThemeButton from "@/components/ThemeButton";
import SearchBox from "@/components/SearchBox";
import Link from "next/link";
import { fetchArticles, fetchCategories } from "@/lib/api";

export default function Home() {
    const [articles, setArticles] = useState([]);
    const [filteredArticles, setFilteredArticles] = useState([]);
    const [categories, setCategories] = useState([]);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
        async function loadData() {
            const articlesData = await fetchArticles();
            const categoriesData = await fetchCategories();

            setArticles(articlesData);
            setFilteredArticles(articlesData);
            setCategories(categoriesData);
            setLoading(false);
        }

        loadData();
    }, []);

    const handleSearch = (query) => {
        if (!query.trim()) {
            setFilteredArticles(articles);
            return;
        }

        const lowercaseQuery = query.toLowerCase();
        const filtered = articles.filter(
            (article) =>
                article.title.toLowerCase().includes(lowercaseQuery) ||
                article.excerpt.toLowerCase().includes(lowercaseQuery),
        );
        setFilteredArticles(filtered);
    };

    if (loading) {
        return <div className="p-8">Lädt Daten...</div>;
    }

    return (
        <main className="p-8">
            <div className="flex justify-between items-center mb-8">
                <h1 className="text-2xl font-bold">Aktuelle Nachrichten</h1>
                <ThemeButton />
            </div>

            <SearchBox onSearch={handleSearch} />

            {/* Rest der Komponente wie zuvor... */}
            <div className="mb-6">
                <h2 className="text-lg font-semibold mb-2">Kategorien:</h2>
                <div className="flex gap-2 flex-wrap">
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

            <div className="grid gap-6">
                {filteredArticles.length > 0 ? (
                    filteredArticles.map((article) => (
                        <NewsCard
                            key={article.id}
                            id={article.id}
                            title={article.title}
                            excerpt={article.excerpt}
                            author={article.author}
                            date={article.date}
                            category={article.category}
                        />
                    ))
                ) : (
                    <p>Keine Ergebnisse gefunden.</p>
                )}
            </div>
        </main>
    );
}
```

</details>

## Nächste Schritte

Super!
Du hast jetzt gelernt, wie du Daten von einer externen API abrufen und in deiner Next.js-Anwendung anzeigen kannst. Du hast auch gesehen, wie du Lade- und Fehlerzustände behandeln kannst. Im nächsten Teil werden wir uns mit dem Styling und der UI deiner Anwendung befassen.
