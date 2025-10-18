# Übung 8: Einfache Suchfunktion

## Ziel

In dieser Übung wirst du eine Suchfunktion zu deinem News-Portal hinzufügen. Du wirst lernen, wie du Client-Komponenten für interaktive Features verwendest und wie man Filter-Funktionalität implementiert.

## Zeit: 45 Minuten

## Schritt 1: Such-Komponente erstellen

Zuerst erstellen wir eine wiederverwendbare Suchkomponente:

1. Erstelle eine neue Datei `SearchBox.jsx` im `components`-Ordner
2. Füge folgenden Code ein:

```jsx
"use client";
import { useState } from "react";

export default function SearchBox({ onSearch }) {
    const [query, setQuery] = useState("");

    const handleSubmit = (e) => {
        e.preventDefault();
        onSearch(query);
    };

    // Suche auch bei Änderungen nach kurzer Verzögerung auslösen (für bessere UX)
    const handleChange = (e) => {
        setQuery(e.target.value);
    };

    return (
        <form onSubmit={handleSubmit} className="mb-6">
            <div className="relative">
                <div className="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none">
                    <svg
                        className="w-4 h-4 text-gray-500"
                        xmlns="http://www.w3.org/2000/svg"
                        fill="none"
                        viewBox="0 0 24 24"
                        stroke="currentColor"
                    >
                        <path
                            strokeLinecap="round"
                            strokeLinejoin="round"
                            strokeWidth={2}
                            d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
                        />
                    </svg>
                </div>

                <input
                    type="text"
                    value={query}
                    onChange={handleChange}
                    className="w-full pl-10 pr-4 py-2 border rounded-lg focus:ring-2 focus:ring-blue-300 focus:border-blue-500 transition-colors"
                    placeholder="Nachrichten suchen..."
                />

                <button
                    type="submit"
                    className="absolute inset-y-0 right-0 px-4 py-2 bg-blue-500 text-white rounded-r-lg hover:bg-blue-600 transition-colors"
                >
                    Suchen
                </button>
            </div>
        </form>
    );
}
```

## Schritt 2: Startseite in eine Client-Komponente umwandeln

Da wir Interaktivität für die Suche benötigen, müssen wir die Startseite in eine Client-Komponente umwandeln:

1. Erstelle eine neue Datei `HomePage.jsx` im `components`-Ordner
2. Füge folgenden Code ein:

```jsx
"use client";
import { useState } from "react";
import NewsCard from "@/components/NewsCard";
import ThemeButton from "@/components/ThemeButton";
import SearchBox from "@/components/SearchBox";
import Link from "next/link";

// Diese Komponente wird clientseitig gerendert
export default function HomePage({ initialPosts, categories }) {
    // State für gefilterte Beiträge
    const [filteredPosts, setFilteredPosts] = useState(initialPosts);
    const [searchQuery, setSearchQuery] = useState("");

    // Suchfunktion
    const handleSearch = (query) => {
        setSearchQuery(query);

        if (!query.trim()) {
            // Wenn die Suche leer ist, zeige alle Beiträge
            setFilteredPosts(initialPosts);
            return;
        }

        // Suche in Titel und Inhalt
        const lowercaseQuery = query.toLowerCase();
        const filtered = initialPosts.filter(
            (post) =>
                post.title.toLowerCase().includes(lowercaseQuery) ||
                post.excerpt.toLowerCase().includes(lowercaseQuery) ||
                post.author.toLowerCase().includes(lowercaseQuery) ||
                post.category.toLowerCase().includes(lowercaseQuery),
        );

        setFilteredPosts(filtered);
    };

    return (
        <main className="p-4 md:p-8 max-w-6xl mx-auto">
            <div className="flex flex-col sm:flex-row sm:justify-between sm:items-center mb-6 md:mb-8 gap-4">
                <h1 className="text-2xl md:text-3xl font-bold">Aktuelle Nachrichten</h1>
                <ThemeButton />
            </div>

            {/* Suchfeld */}
            <SearchBox onSearch={handleSearch} />

            {/* Kategorienliste */}
            <div className="mb-6 md:mb-8">
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

            {/* Suchergebnisse-Status */}
            {searchQuery && (
                <div className="mb-6">
                    <p className="text-gray-700">
                        {filteredPosts.length === 0
                            ? `Keine Ergebnisse für "${searchQuery}" gefunden.`
                            : `${filteredPosts.length} Ergebnis${filteredPosts.length !== 1 ? "se" : ""} für "${searchQuery}" gefunden.`}
                    </p>
                </div>
            )}

            {/* Nachrichtenliste - Responsive Grid */}
            {filteredPosts.length > 0 ? (
                <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
                    {filteredPosts.map((post) => (
                        <NewsCard
                            key={post.id}
                            id={post.id}
                            title={post.title}
                            excerpt={post.excerpt}
                            author={post.author}
                            date={post.date}
                            category={post.category}
                        />
                    ))}
                </div>
            ) : (
                <div className="bg-white rounded-lg shadow p-8 text-center">
                    <svg
                        className="w-16 h-16 mx-auto text-gray-400 mb-4"
                        xmlns="http://www.w3.org/2000/svg"
                        fill="none"
                        viewBox="0 0 24 24"
                        stroke="currentColor"
                    >
                        <path
                            strokeLinecap="round"
                            strokeLinejoin="round"
                            strokeWidth={2}
                            d="M9.172 16.172a4 4 0 015.656 0M9 10h.01M15 10h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
                        />
                    </svg>
                    <h3 className="text-lg font-semibold mb-2">Keine Ergebnisse gefunden</h3>
                    <p className="text-gray-600 mb-4">
                        Versuche es mit anderen Suchbegriffen oder durchstöbere die Kategorien.
                    </p>
                    <button
                        onClick={() => handleSearch("")}
                        className="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 transition-colors"
                    >
                        Alle Nachrichten anzeigen
                    </button>
                </div>
            )}
        </main>
    );
}
```

## Schritt 3: Hauptseite aktualisieren

Jetzt aktualisieren wir die Hauptseite, um unsere neue Client-Komponente zu verwenden:

1. Öffne die Datei `app/page.js`
2. Ersetze den Inhalt mit folgendem Code:

```jsx
import HomePage from "@/components/HomePage";
import { fetchPosts, fetchCategories } from "@/lib/api";

// Diese Komponente wird auf dem Server ausgeführt
export default async function Home() {
    // Daten von der API abrufen
    const posts = await fetchPosts();
    const categories = await fetchCategories();

    // Server-Komponente rendert Client-Komponente mit initialen Daten
    return <HomePage initialPosts={posts} categories={categories} />;
}
```

## Schritt 4: Suchhistorie hinzufügen

Lass uns die Suchfunktion erweitern, um eine Suchhistorie anzuzeigen:

1. Aktualisiere die `HomePage.jsx` Komponente, um Suchhistorie zu unterstützen:

```jsx
"use client";
import { useState, useEffect } from "react";
import NewsCard from "@/components/NewsCard";
import ThemeButton from "@/components/ThemeButton";
import SearchBox from "@/components/SearchBox";
import Link from "next/link";

export default function HomePage({ initialPosts, categories }) {
    const [filteredPosts, setFilteredPosts] = useState(initialPosts);
    const [searchQuery, setSearchQuery] = useState("");
    // Neue State für Suchhistorie
    const [searchHistory, setSearchHistory] = useState([]);

    // Suchhistorie aus localStorage laden (nur auf Client-Seite)
    useEffect(() => {
        const savedHistory = localStorage.getItem("searchHistory");
        if (savedHistory) {
            try {
                setSearchHistory(JSON.parse(savedHistory));
            } catch (e) {
                console.error("Fehler beim Laden der Suchhistorie:", e);
            }
        }
    }, []);

    // Suchfunktion
    const handleSearch = (query) => {
        setSearchQuery(query);

        // Suchbegriff zur Historie hinzufügen, wenn nicht leer
        if (query.trim() && !searchHistory.includes(query)) {
            const newHistory = [query, ...searchHistory.slice(0, 4)]; // Beschränke auf 5 Einträge
            setSearchHistory(newHistory);
            localStorage.setItem("searchHistory", JSON.stringify(newHistory));
        }

        if (!query.trim()) {
            // Wenn die Suche leer ist, zeige alle Beiträge
            setFilteredPosts(initialPosts);
            return;
        }

        // Suche in Titel und Inhalt
        const lowercaseQuery = query.toLowerCase();
        const filtered = initialPosts.filter(
            (post) =>
                post.title.toLowerCase().includes(lowercaseQuery) ||
                post.excerpt.toLowerCase().includes(lowercaseQuery) ||
                post.author.toLowerCase().includes(lowercaseQuery) ||
                post.category.toLowerCase().includes(lowercaseQuery),
        );

        setFilteredPosts(filtered);
    };

    // Funktion zum Löschen der Suchhistorie
    const clearSearchHistory = () => {
        setSearchHistory([]);
        localStorage.removeItem("searchHistory");
    };

    return (
        <main className="p-4 md:p-8 max-w-6xl mx-auto">
            <div className="flex flex-col sm:flex-row sm:justify-between sm:items-center mb-6 md:mb-8 gap-4">
                <h1 className="text-2xl md:text-3xl font-bold">Aktuelle Nachrichten</h1>
                <ThemeButton />
            </div>

            {/* Suchfeld */}
            <SearchBox onSearch={handleSearch} />

            {/* Suchhistorie anzeigen */}
            {searchHistory.length > 0 && (
                <div className="mb-4 flex flex-wrap items-center gap-2 text-sm">
                    <span className="text-gray-600">Letzte Suchen:</span>
                    {searchHistory.map((term, index) => (
                        <button
                            key={index}
                            onClick={() => handleSearch(term)}
                            className="px-2 py-1 bg-gray-100 hover:bg-gray-200 rounded-md transition-colors"
                        >
                            {term}
                        </button>
                    ))}
                    <button onClick={clearSearchHistory} className="text-red-500 hover:text-red-700 text-sm ml-2">
                        Löschen
                    </button>
                </div>
            )}

            {/* Rest der Komponente wie zuvor... */}
            {/* ... */}
        </main>
    );
}
```

## Schritt 5: Erweiterte Suchfilter hinzufügen (Aufgabe)

Erweitere die Suchfunktionalität, um nach Kategorien und Autoren zu filtern:

1. Ergänze die `SearchBox`-Komponente um Dropdown-Filter
2. Implementiere die Filterlogik in der `HomePage`-Komponente

<details>
<summary>Hilfe zur Lösung anzeigen</summary>

Aktualisiere zuerst die `SearchBox.jsx`:

```jsx
"use client";
import { useState } from "react";

export default function SearchBox({ onSearch, categories, authors }) {
    const [query, setQuery] = useState("");
    const [categoryFilter, setCategoryFilter] = useState("");
    const [authorFilter, setAuthorFilter] = useState("");

    const handleSubmit = (e) => {
        e.preventDefault();
        onSearch({
            query,
            category: categoryFilter,
            author: authorFilter,
        });
    };

    return (
        <form onSubmit={handleSubmit} className="mb-6">
            <div className="flex flex-col md:flex-row gap-3">
                {/* Suchfeld */}
                <div className="relative flex-grow">
                    <div className="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none">
                        <svg
                            className="w-4 h-4 text-gray-500"
                            xmlns="http://www.w3.org/2000/svg"
                            fill="none"
                            viewBox="0 0 24 24"
                            stroke="currentColor"
                        >
                            <path
                                strokeLinecap="round"
                                strokeLinejoin="round"
                                strokeWidth={2}
                                d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
                            />
                        </svg>
                    </div>

                    <input
                        type="text"
                        value={query}
                        onChange={(e) => setQuery(e.target.value)}
                        className="w-full pl-10 pr-4 py-2 border rounded-lg focus:ring-2 focus:ring-blue-300 focus:border-blue-500 transition-colors"
                        placeholder="Nachrichten suchen..."
                    />
                </div>

                {/* Kategorie Filter */}
                {categories && (
                    <select
                        value={categoryFilter}
                        onChange={(e) => setCategoryFilter(e.target.value)}
                        className="border rounded-lg px-3 py-2 focus:ring-2 focus:ring-blue-300 focus:border-blue-500 transition-colors"
                    >
                        <option value="">Alle Kategorien</option>
                        {categories.map((category) => (
                            <option key={category} value={category}>
                                {category}
                            </option>
                        ))}
                    </select>
                )}

                {/* Autoren Filter */}
                {authors && (
                    <select
                        value={authorFilter}
                        onChange={(e) => setAuthorFilter(e.target.value)}
                        className="border rounded-lg px-3 py-2 focus:ring-2 focus:ring-blue-300 focus:border-blue-500 transition-colors"
                    >
                        <option value="">Alle Autoren</option>
                        {authors.map((author) => (
                            <option key={author} value={author}>
                                {author}
                            </option>
                        ))}
                    </select>
                )}

                <button
                    type="submit"
                    className="px-6 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors"
                >
                    Suchen
                </button>
            </div>
        </form>
    );
}
```

Dann aktualisiere die `HomePage.jsx` Komponente:

```jsx
// Hilfsfunktion zum Extrahieren eindeutiger Autoren
const getUniqueAuthors = (posts) => {
    return [...new Set(posts.map((post) => post.author))];
};

export default function HomePage({ initialPosts, categories }) {
    const [filteredPosts, setFilteredPosts] = useState(initialPosts);
    const [searchQuery, setSearchQuery] = useState("");
    const [searchHistory, setSearchHistory] = useState([]);
    const [activeFilters, setActiveFilters] = useState({
        query: "",
        category: "",
        author: "",
    });

    // Eindeutige Autorenliste
    const authors = getUniqueAuthors(initialPosts);

    // Erweiterte Suchfunktion
    const handleSearch = (filters) => {
        setActiveFilters(filters);

        const { query, category, author } = filters;
        let filtered = [...initialPosts];

        // Text-Suche
        if (query.trim()) {
            const lowercaseQuery = query.toLowerCase();
            filtered = filtered.filter(
                (post) =>
                    post.title.toLowerCase().includes(lowercaseQuery) ||
                    post.excerpt.toLowerCase().includes(lowercaseQuery),
            );
        }

        // Kategorie-Filter
        if (category) {
            filtered = filtered.filter((post) => post.category === category);
        }

        // Autor-Filter
        if (author) {
            filtered = filtered.filter((post) => post.author === author);
        }

        setFilteredPosts(filtered);
    };

    // Rest der Komponente...

    return (
        <main className="p-4 md:p-8 max-w-6xl mx-auto">
            {/* ... */}

            {/* Erweiterte Suchbox */}
            <SearchBox onSearch={handleSearch} categories={categories} authors={authors} />

            {/* ... Rest wie zuvor */}
        </main>
    );
}
```

</details>

## Nächste Schritte

Gratulation!
Du hast erfolgreich eine Suchfunktion in dein Next.js News-Portal integriert. Diese Funktionalität verbessert die Benutzererfahrung erheblich, indem sie es den Benutzern ermöglicht, schnell relevante Inhalte zu finden.

### Du könntest dieses Projekt noch weiter ausbauen mit:

- Benutzerauthentifizierung
- Kommentarfunktion
- Lesezeichen für Artikel
- Newsletter-Anmeldung
- Und vielen weiteren Features!

Dein News-Portal ist jetzt bereit für die reale Welt und demonstriert viele wichtige Konzepte von Next.js und modernem React-Development.
