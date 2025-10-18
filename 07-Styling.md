# Übung 7: Styling mit Tailwind CSS

## Ziel

In dieser Übung wirst du deine News-Portal-Anwendung mit Tailwind CSS ansprechender gestalten. Du wirst ein responsives Design erstellen und verschiedene Styling-Techniken anwenden.

## Zeit: 45 Minuten

## Schritt 1: Verständnis von Tailwind CSS

Tailwind CSS ist ein Utility-first CSS-Framework, das bereits in deinem Projekt eingerichtet ist. Es erlaubt dir, Elemente direkt in deinem HTML mit Klassen zu gestalten, anstatt separate CSS-Dateien zu schreiben.

Hauptvorteile von Tailwind CSS:

- Schnelle Entwicklung durch vordefinierte Utility-Klassen
- Konsistentes Design-System mit Abständen, Farben, usw.
- Responsive Design durch Breakpoint-Prefixe (sm:, md:, lg:, xl:)
- Keine Notwendigkeit für eigene CSS-Klassen zu erfinden

## Schritt 2: Responsives Layout erstellen

Jetzt verbessern wir das Layout, um es auf verschiedenen Bildschirmgrößen gut aussehen zu lassen:

1. Öffne die Datei `app/page.js`
2. Aktualisiere das Grid-Layout für die Nachrichtenliste:

```jsx
// ... bestehender Import-Code ...

export default async function Home() {
    const posts = await fetchPosts();
    const categories = await fetchCategories();

    return (
        <main className="p-4 md:p-8 max-w-6xl mx-auto">
            <div className="flex flex-col sm:flex-row sm:justify-between sm:items-center mb-6 md:mb-8 gap-4">
                <h1 className="text-2xl md:text-3xl font-bold">Aktuelle Nachrichten</h1>
                <ThemeButton />
            </div>

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

            {/* Nachrichtenliste - Responsive Grid */}
            <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
                {posts.map((post) => (
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
        </main>
    );
}
```

## Schritt 3: NewsCard verbessern

Jetzt verbessern wir das Design der NewsCard-Komponente:

1. Öffne die Datei `components/NewsCard.jsx`
2. Ersetze den Inhalt mit folgendem Code:

```jsx
import Link from "next/link";
import LikeButton from "./LikeButton";

export default function NewsCard({ id, title, excerpt, author, date, category }) {
    // Formatiere das Datum als lesbare Zeichenkette
    const formattedDate = new Date(date).toLocaleDateString("de-DE", {
        year: "numeric",
        month: "long",
        day: "numeric",
    });

    // Kürze zu lange Titel
    const truncatedTitle = title.length > 60 ? title.substring(0, 60) + "..." : title;

    return (
        <Link href={`/news/${id}`} className="group block h-full">
            <article className="border rounded-lg shadow-sm bg-white hover:shadow-md transition-all duration-300 h-full flex flex-col p-0 overflow-hidden group-hover:border-blue-300">
                {/* Farbiger Balken oben basierend auf Kategorie */}
                <div className={`h-2 w-full ${getCategoryColor(category)}`}></div>

                <div className="p-4 flex-grow flex flex-col">
                    <h2 className="text-xl font-bold text-gray-800 group-hover:text-blue-600 transition-colors mb-2">
                        {truncatedTitle}
                    </h2>

                    {/* Kategorie-Badge */}
                    {category && (
                        <Link
                            href={`/category/${category}`}
                            className="self-start inline-block mt-1 mb-3 px-2 py-1 text-xs bg-blue-100 text-blue-800 rounded-full group-hover:bg-blue-200 transition-colors"
                            onClick={(e) => e.stopPropagation()}
                        >
                            {category}
                        </Link>
                    )}

                    <p className="text-gray-600 mb-4 flex-grow">{excerpt}</p>

                    <div className="mt-auto flex justify-between items-center pt-3 border-t border-gray-100">
                        <div className="text-sm text-gray-500">
                            <span>{author}</span>
                            <span className="mx-1">•</span>
                            <span>{formattedDate}</span>
                        </div>

                        <div onClick={(e) => e.stopPropagation()}>
                            <LikeButton />
                        </div>
                    </div>
                </div>
            </article>
        </Link>
    );
}

// Hilfsfunktion für Kategorie-Farben
function getCategoryColor(category) {
    const colors = {
        Technologie: "bg-blue-500",
        Nachrichten: "bg-red-500",
        Web: "bg-purple-500",
        Design: "bg-pink-500",
        Programmierung: "bg-green-500",
        Framework: "bg-yellow-500",
        React: "bg-cyan-500",
        CSS: "bg-indigo-500",
        JavaScript: "bg-orange-500",
        KI: "bg-emerald-500",
    };

    return colors[category] || "bg-gray-500";
}
```

## Schritt 4: Detailseite verbessern

Jetzt verbessern wir auch die Detailseite für Artikel:

1. Öffne die Datei `app/news/[id]/page.js`
2. Ersetze den Inhalt mit folgendem Code:

```jsx
import { fetchPost } from "@/lib/api";
import Link from "next/link";

export default async function NewsDetail({ params }) {
    const post = await fetchPost(params.id);

    if (!post) {
        return (
            <div className="p-8 max-w-3xl mx-auto text-center">
                <h1 className="text-2xl font-bold mb-4 text-red-600">Artikel nicht gefunden</h1>
                <p className="mb-6 text-gray-600">Der gesuchte Artikel existiert nicht oder wurde entfernt.</p>
                <Link
                    href="/"
                    className="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 transition-colors inline-block"
                >
                    ← Zurück zur Übersicht
                </Link>
            </div>
        );
    }

    const formattedDate = new Date(post.date).toLocaleDateString("de-DE", {
        year: "numeric",
        month: "long",
        day: "numeric",
    });

    // Funktion für Kategoriefarben (könnte in eine Hilfsdatei ausgelagert werden)
    const getCategoryColor = (category) => {
        const colors = {
            Technologie: "bg-blue-500 text-white",
            Nachrichten: "bg-red-500 text-white",
            Web: "bg-purple-500 text-white",
            Design: "bg-pink-500 text-white",
            Programmierung: "bg-green-500 text-white",
            Framework: "bg-yellow-500 text-white",
            React: "bg-cyan-500 text-white",
            CSS: "bg-indigo-500 text-white",
            JavaScript: "bg-orange-500 text-white",
            KI: "bg-emerald-500 text-white",
        };

        return colors[category] || "bg-gray-500 text-white";
    };

    return (
        <article className="p-4 md:p-8 max-w-3xl mx-auto">
            {/* Kategorie-Badge */}
            <Link
                href={`/category/${post.category}`}
                className={`inline-block mb-4 px-3 py-1 rounded-full text-sm ${getCategoryColor(post.category)}`}
            >
                {post.category}
            </Link>

            <h1 className="text-2xl md:text-3xl font-bold mb-4">{post.title}</h1>

            <div className="mb-6 text-gray-500 flex items-center">
                <div className="w-8 h-8 bg-gray-300 rounded-full mr-2 flex items-center justify-center text-white font-bold">
                    {post.author.charAt(0)}
                </div>
                <span>{post.author}</span>
                <span className="mx-2">•</span>
                <span>{formattedDate}</span>
            </div>

            <div className="prose prose-lg max-w-none mb-8">
                {post.content.split("\n\n").map((paragraph, index) => (
                    <p key={index} className="mb-4 text-gray-700 leading-relaxed">
                        {paragraph}
                    </p>
                ))}
            </div>

            <Link
                href="/"
                className="inline-flex items-center gap-2 text-blue-500 hover:text-blue-700 transition-colors"
            >
                <svg
                    xmlns="http://www.w3.org/2000/svg"
                    width="20"
                    height="20"
                    viewBox="0 0 24 24"
                    fill="none"
                    stroke="currentColor"
                    strokeWidth="2"
                    strokeLinecap="round"
                    strokeLinejoin="round"
                >
                    <path d="M19 12H5M12 19l-7-7 7-7" />
                </svg>
                Zurück zur Übersicht
            </Link>
        </article>
    );
}
```

## Schritt 5: Navigation und Layout verbessern

Lass uns jetzt das Hauptlayout und die Navigation verbessern:

1. Öffne die Datei `app/layout.js`
2. Ersetze den Inhalt mit folgendem Code:

```jsx
import Navbar from "@/components/Navbar";
import "./globals.css";

export const metadata = {
    title: "News-Portal | Next.js Demo",
    description: "Ein modernes News-Portal entwickelt mit Next.js und Tailwind CSS",
};

export default function RootLayout({ children }) {
    const currentYear = new Date().getFullYear();

    return (
        <html lang="de">
            <body className="bg-gray-50 min-h-screen flex flex-col">
                <header className="bg-white border-b shadow-sm sticky top-0 z-10">
                    <div className="container mx-auto px-4">
                        <div className="flex justify-between items-center h-16">
                            <div className="flex items-center gap-2">
                                <div className="w-8 h-8 rounded bg-blue-600 flex items-center justify-center text-white font-bold">
                                    N
                                </div>
                                <span className="text-xl font-bold">NewsPortal</span>
                            </div>

                            <nav className="hidden md:block">
                                <Navbar />
                            </nav>
                        </div>
                    </div>
                </header>

                <main className="flex-grow py-6">{children}</main>

                <footer className="mt-auto bg-gray-800 text-gray-300 py-8">
                    <div className="container mx-auto px-4">
                        <div className="grid md:grid-cols-3 gap-8">
                            <div>
                                <h3 className="text-lg font-semibold mb-4">Über uns</h3>
                                <p>
                                    NewsPortal ist ein Demo-Projekt für ein Next.js Seminar. Die Plattform zeigt die
                                    Möglichkeiten des modernen Web-Frameworks.
                                </p>
                            </div>

                            <div>
                                <h3 className="text-lg font-semibold mb-4">Kategorien</h3>
                                <ul className="space-y-2">
                                    <li>
                                        <a href="/category/Technologie" className="hover:text-white">
                                            Technologie
                                        </a>
                                    </li>
                                    <li>
                                        <a href="/category/Web" className="hover:text-white">
                                            Web
                                        </a>
                                    </li>
                                    <li>
                                        <a href="/category/Design" className="hover:text-white">
                                            Design
                                        </a>
                                    </li>
                                </ul>
                            </div>

                            <div>
                                <h3 className="text-lg font-semibold mb-4">Kontakt</h3>
                                <p>Email: info@newsportal-demo.de</p>
                                <p>Telefon: +49 123 456789</p>
                            </div>
                        </div>

                        <div className="border-t border-gray-700 mt-8 pt-4 text-center text-sm">
                            &copy; {currentYear} NewsPortal - Ein Next.js Projekt
                        </div>
                    </div>
                </footer>

                {/* Mobile Navigation */}
                <div className="md:hidden fixed bottom-0 left-0 right-0 bg-white border-t py-2 px-4 z-10">
                    <Navbar isMobile={true} />
                </div>
            </body>
        </html>
    );
}
```

3. Öffne die Datei `components/Navbar.jsx`
4. Ersetze den Inhalt mit folgendem Code:

```jsx
"use client";
import Link from "next/link";
import { usePathname } from "next/navigation";

export default function Navbar({ isMobile = false }) {
    const pathname = usePathname();

    const navItems = [
        { name: "Startseite", path: "/" },
        { name: "Über uns", path: "/about" },
        { name: "Kontakt", path: "/contact" },
    ];

    // Funktion zum Bestimmen des Klassenstils basierend auf aktivem Link
    const getLinkClass = (path) => {
        const baseClass = isMobile ? "flex flex-col items-center text-xs" : "px-3 py-2 rounded-md text-sm font-medium";

        const activeClass = isMobile ? "text-blue-600" : "bg-blue-50 text-blue-600";

        const inactiveClass = isMobile
            ? "text-gray-600 hover:text-blue-600"
            : "text-gray-700 hover:bg-gray-100 hover:text-gray-900";

        return `${baseClass} transition-colors ${pathname === path ? activeClass : inactiveClass}`;
    };

    // Icons für mobile Navigation
    const getIcon = (name) => {
        switch (name) {
            case "Startseite":
                return (
                    <svg
                        xmlns="http://www.w3.org/2000/svg"
                        width="24"
                        height="24"
                        viewBox="0 0 24 24"
                        fill="none"
                        stroke="currentColor"
                        strokeWidth="2"
                        strokeLinecap="round"
                        strokeLinejoin="round"
                    >
                        <path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path>
                        <polyline points="9 22 9 12 15 12 15 22"></polyline>
                    </svg>
                );
            case "Über uns":
                return (
                    <svg
                        xmlns="http://www.w3.org/2000/svg"
                        width="24"
                        height="24"
                        viewBox="0 0 24 24"
                        fill="none"
                        stroke="currentColor"
                        strokeWidth="2"
                        strokeLinecap="round"
                        strokeLinejoin="round"
                    >
                        <circle cx="12" cy="12" r="10"></circle>
                        <line x1="12" y1="16" x2="12" y2="12"></line>
                        <line x1="12" y1="8" x2="12.01" y2="8"></line>
                    </svg>
                );
            case "Kontakt":
                return (
                    <svg
                        xmlns="http://www.w3.org/2000/svg"
                        width="24"
                        height="24"
                        viewBox="0 0 24 24"
                        fill="none"
                        stroke="currentColor"
                        strokeWidth="2"
                        strokeLinecap="round"
                        strokeLinejoin="round"
                    >
                        <path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z"></path>
                    </svg>
                );
            default:
                return null;
        }
    };

    return isMobile ? (
        // Mobile Navigation (Bottom Bar)
        <div className="flex justify-around">
            {navItems.map((item) => (
                <Link key={item.path} href={item.path} className={getLinkClass(item.path)}>
                    {getIcon(item.name)}
                    <span>{item.name}</span>
                </Link>
            ))}
        </div>
    ) : (
        // Desktop Navigation
        <div className="flex gap-1">
            {navItems.map((item) => (
                <Link key={item.path} href={item.path} className={getLinkClass(item.path)}>
                    {item.name}
                </Link>
            ))}
        </div>
    );
}
```

## Schritt 6: Design für Dark Mode (Aufgabe)

Implementiere eine einfache Dark Mode-Unterstützung für die Anwendung:

1. Aktualisiere den ThemeButton, um einen tatsächlichen Dark Mode zu implementieren
2. Füge Dark Mode-Klassen zu den wichtigsten Komponenten hinzu

<details>
<summary>Hilfe zur Lösung anzeigen</summary>

Zuerst aktualisiere die ThemeButton-Komponente:

```jsx
"use client";
import { useState, useEffect } from "react";

export default function ThemeButton() {
    const [isDark, setIsDark] = useState(false);

    // Beim ersten Laden den gespeicherten Theme-Status abrufen
    useEffect(() => {
        // Prüfen, ob Dark Mode bereits aktiviert ist (im localStorage oder durch Systemeinstellung)
        const isDarkMode =
            localStorage.getItem("theme") === "dark" ||
            (window.matchMedia("(prefers-color-scheme: dark)").matches && localStorage.getItem("theme") !== "light");

        setIsDark(isDarkMode);

        // Klasse auf dem HTML-Element setzen
        if (isDarkMode) {
            document.documentElement.classList.add("dark");
        } else {
            document.documentElement.classList.remove("dark");
        }
    }, []);

    const toggleTheme = () => {
        const newTheme = !isDark;
        setIsDark(newTheme);

        // HTML-Klasse und localStorage aktualisieren
        if (newTheme) {
            document.documentElement.classList.add("dark");
            localStorage.setItem("theme", "dark");
        } else {
            document.documentElement.classList.remove("dark");
            localStorage.setItem("theme", "light");
        }
    };

    return (
        <button
            onClick={toggleTheme}
            className={`p-2 rounded-full ${isDark ? "bg-yellow-400 text-gray-900" : "bg-gray-800 text-white"}`}
            aria-label={isDark ? "Zum hellen Modus wechseln" : "Zum dunklen Modus wechseln"}
        >
            {isDark ? (
                <svg
                    xmlns="http://www.w3.org/2000/svg"
                    width="20"
                    height="20"
                    viewBox="0 0 24 24"
                    fill="none"
                    stroke="currentColor"
                    strokeWidth="2"
                    strokeLinecap="round"
                    strokeLinejoin="round"
                >
                    <circle cx="12" cy="12" r="5"></circle>
                    <line x1="12" y1="1" x2="12" y2="3"></line>
                    <line x1="12" y1="21" x2="12" y2="23"></line>
                    <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"></line>
                    <line x1="18.36" y1="18.36" x2="19.78" y2="19.78"></line>
                    <line x1="1" y1="12" x2="3" y2="12"></line>
                    <line x1="21" y1="12" x2="23" y2="12"></line>
                    <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"></line>
                    <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"></line>
                </svg>
            ) : (
                <svg
                    xmlns="http://www.w3.org/2000/svg"
                    width="20"
                    height="20"
                    viewBox="0 0 24 24"
                    fill="none"
                    stroke="currentColor"
                    strokeWidth="2"
                    strokeLinecap="round"
                    strokeLinejoin="round"
                >
                    <path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"></path>
                </svg>
            )}
        </button>
    );
}
```

Dann aktualisiere das Layout, um Dark Mode-Klassen hinzuzufügen:

```jsx
<html lang="de" className="scroll-smooth">
  <body className="bg-gray-50 dark:bg-gray-900 dark:text-white min-h-screen flex flex-col transition-colors duration-300">
    <header className="bg-white dark:bg-gray-800 border-b shadow-sm sticky top-0 z-10">
      {/* ... */}
    </header>
```

Und auch die NewsCard:

```jsx
<article className="border rounded-lg shadow-sm bg-white dark:bg-gray-800 dark:border-gray-700 hover:shadow-md transition-all duration-300 h-full flex flex-col p-0 overflow-hidden group-hover:border-blue-300">
    {/* ... */}
    <h2 className="text-xl font-bold text-gray-800 dark:text-gray-100 group-hover:text-blue-600 transition-colors mb-2">
        {truncatedTitle}
    </h2>
    {/* ... */}
    <p className="text-gray-600 dark:text-gray-300 mb-4 flex-grow">{excerpt}</p>
    {/* ... */}
</article>
```

Vergiss nicht, die Tailwind-Konfiguration in `tailwind.config.js` zu aktualisieren:

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
    darkMode: "class", // Aktiviert klassen-basierten Dark Mode
    content: [
        "./pages/**/*.{js,ts,jsx,tsx,mdx}",
        "./components/**/*.{js,ts,jsx,tsx,mdx}",
        "./app/**/*.{js,ts,jsx,tsx,mdx}",
    ],
    theme: {
        extend: {},
    },
    plugins: [],
};
```

</details>

## Nächste Schritte

Super!
Du hast jetzt ein ansprechendes, responsives Design für dein News-Portal erstellt. Die Anwendung sieht jetzt professioneller aus und bietet eine gute Benutzererfahrung auf allen Geräten. Im nächsten Schritt könntest du weitere Funktionen wie eine Suchfunktion oder Benutzerprofile hinzufügen.
