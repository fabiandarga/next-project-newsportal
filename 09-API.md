# Übung: Erstellung einer RESTful API mit Next.js Route Handlers

## Einführung

In dieser Übung wirst Du die Backend-Funktionalität für Dein "NewsPortal"-Projekt entwickeln. Nachdem Du bereits das Frontend-Formular zum Hinzufügen von Nachrichtenartikeln erstellt hast, sollst Du nun eine RESTful API implementieren, die das Abrufen (GET) und Erstellen (POST) von Nachrichtenartikeln ermöglicht. Dazu nutzt Du die Route Handlers von Next.js, die eine elegante Methode bieten, um API-Routen in Deiner Next.js-Anwendung zu erstellen.

## Lernziele

- Erstellung von API-Routen mit Next.js Route Handlers
- Implementierung von GET- und POST-Methoden für Ressourcen
- Validierung von Anfragedaten mit Zod
- Implementierung effektiver Fehlerbehandlung
- Verständnis der RESTful-API-Prinzipien

## Teilaufgabe 1: Projektstruktur für API-Routen einrichten

Next.js bietet ein intuitives Routing-System, auch für API-Routen. Wir werden die Route Handler API von Next.js verwenden, die mit dem App Router eingeführt wurde.

1. Erstelle einen neuen Ordner in Deinem Projekt: `/app/api/news`

2. Erstelle eine Datei für den Route Handler: `/app/api/news/route.js` (oder `.ts` für TypeScript)

```jsx
// /app/api/news/route.js
import { NextResponse } from "next/server";

// GET-Handler für /api/news
export async function GET(request) {
    try {
        // Hier werden wir später die Nachrichtenartikel abrufen

        // Temporäre Daten für die Entwicklung
        const news = [
            { id: 1, title: "Erster Artikel", text: "Dies ist der Inhalt des ersten Artikels.", category: "politik" },
            {
                id: 2,
                title: "Zweiter Artikel",
                text: "Dies ist der Inhalt des zweiten Artikels.",
                category: "wirtschaft",
            },
        ];

        // Erfolgreiche Antwort zurückgeben
        return NextResponse.json({ data: news }, { status: 200 });
    } catch (error) {
        // Fehlerbehandlung
        console.error("Fehler beim Abrufen der Nachrichtenartikel:", error);

        return NextResponse.json(
            { error: "Beim Abrufen der Nachrichtenartikel ist ein Fehler aufgetreten." },
            { status: 500 },
        );
    }
}

// POST-Handler für /api/news
export async function POST(request) {
    try {
        // Hier werden wir später die Anfragedaten validieren und speichern
        const body = await request.json();

        // Hier könnten wir die Daten in einer Datenbank speichern
        console.log("Empfangene Daten:", body);

        // Erfolgreiche Antwort zurückgeben
        return NextResponse.json(
            {
                message: "Nachrichtenartikel erfolgreich erstellt",
                article: { ...body, id: Math.floor(Math.random() * 1000) }, // Simuliere eine ID-Generierung
            },
            { status: 201 },
        );
    } catch (error) {
        // Fehlerbehandlung
        console.error("Fehler beim Erstellen eines Nachrichtenartikels:", error);

        return NextResponse.json(
            { error: "Beim Erstellen des Nachrichtenartikels ist ein Fehler aufgetreten." },
            { status: 500 },
        );
    }
}
```

## Teilaufgabe 2: Einzelne Nachrichtenartikel abrufen (GET by ID)

Um einzelne Nachrichtenartikel abzurufen, benötigen wir eine weitere Route. In Next.js App Router kannst Du dynamische Routen mit Ordnern in eckigen Klammern erstellen.

1. Erstelle einen neuen Ordner: `/app/api/news/[id]`

2. Erstelle eine Datei für den Route Handler: `/app/api/news/[id]/route.js`

```jsx
// /app/api/news/[id]/route.js
import { NextResponse } from "next/server";

// GET-Handler für /api/news/[id]
export async function GET(request, { params }) {
    try {
        // ID aus den URL-Parametern extrahieren
        const id = params.id;

        // Hier würdest Du normalerweise einen Artikel aus der Datenbank abrufen
        // Für die Übung verwenden wir temporäre Daten
        const news = [
            { id: "1", title: "Erster Artikel", text: "Dies ist der Inhalt des ersten Artikels.", category: "politik" },
            {
                id: "2",
                title: "Zweiter Artikel",
                text: "Dies ist der Inhalt des zweiten Artikels.",
                category: "wirtschaft",
            },
        ];

        // Artikel mit der gegebenen ID suchen
        const article = news.find((item) => item.id === id);

        if (!article) {
            // Wenn kein Artikel gefunden wurde, geben wir einen 404-Fehler zurück
            return NextResponse.json({ error: "Nachrichtenartikel nicht gefunden" }, { status: 404 });
        }

        // Erfolgreiche Antwort zurückgeben
        return NextResponse.json({ data: article }, { status: 200 });
    } catch (error) {
        console.error("Fehler beim Abrufen des Nachrichtenartikels:", error);

        return NextResponse.json(
            { error: "Beim Abrufen des Nachrichtenartikels ist ein Fehler aufgetreten." },
            { status: 500 },
        );
    }
}
```

## Teilaufgabe 3: Implementierung der Datenvalidierung mit Zod

Um sicherzustellen, dass die empfangenen Daten korrekt sind, implementieren wir eine Validierung mit Zod. Zod ist eine TypeScript-first Schema-Validierungsbibliothek, die bereits für die Formularvalidierung im Frontend verwendet wurde.

1. Falls noch nicht geschehen, installiere Zod:

```bash
npm install zod
```

2. Aktualisiere den POST-Handler in `/app/api/news/route.js`:

```jsx
// /app/api/news/route.js - Aktualisierte Version mit Zod-Validierung
import { NextResponse } from "next/server";
import { z } from "zod";

// Validierungsschema für Nachrichtenartikel
const articleSchema = z.object({
    title: z.string().min(5, "Der Titel muss mindestens 5 Zeichen lang sein."),
    text: z.string().min(20, "Der Inhalt muss mindestens 20 Zeichen lang sein."),
    category: z.enum(["politik", "wirtschaft", "technologie", "sport", "kultur"], {
        errorMap: () => ({ message: "Bitte wähle eine gültige Kategorie." }),
    }),
});

// GET-Handler bleibt unverändert...

// POST-Handler mit Validierung
export async function POST(request) {
    try {
        // Anfragedaten einlesen
        const body = await request.json();

        // Validierung durchführen
        const validationResult = articleSchema.safeParse(body);

        // Wenn die Validierung fehlschlägt, geben wir einen 400-Fehler zurück
        if (!validationResult.success) {
            // Fehlermeldungen extrahieren
            const errors = validationResult.error.errors.map((error) => ({
                path: error.path.join("."),
                message: error.message,
            }));

            return NextResponse.json(
                {
                    error: "Validierungsfehler",
                    details: errors,
                },
                { status: 400 },
            );
        }

        // Validierte Daten extrahieren
        const validatedData = validationResult.data;

        // Hier würdest Du normalerweise die Daten in der Datenbank speichern
        // Für die Übung simulieren wir eine ID-Generierung
        const newArticle = {
            ...validatedData,
            id: Math.floor(Math.random() * 1000),
            createdAt: new Date().toISOString(),
        };

        // Erfolgreiche Antwort zurückgeben
        return NextResponse.json(
            {
                message: "Nachrichtenartikel erfolgreich erstellt",
                article: newArticle,
            },
            { status: 201 },
        );
    } catch (error) {
        console.error("Fehler beim Erstellen eines Nachrichtenartikels:", error);

        // Unterscheide zwischen verschiedenen Fehlertypen
        if (error instanceof SyntaxError) {
            return NextResponse.json({ error: "Ungültiges JSON-Format." }, { status: 400 });
        }

        return NextResponse.json(
            { error: "Beim Erstellen des Nachrichtenartikels ist ein Fehler aufgetreten." },
            { status: 500 },
        );
    }
}
```

## Teilaufgabe 4: Integration mit einer Mock-Datenbank

Da wir noch keine echte Datenbank verwenden, erstellen wir eine einfache In-Memory-Datenbank, um das Abrufen und Speichern von Daten zu simulieren.

1. Erstelle eine neue Datei: `/lib/mock-db.js`

```jsx
// /lib/mock-db.js
// Einfache In-Memory-Datenbank für die Übung
let articles = [
    {
        id: "1",
        title: "Neue Technologie-Initiative gestartet",
        text: "Die Regierung hat heute eine neue Initiative zur Förderung von Technologie-Startups angekündigt...",
        category: "technologie",
        createdAt: "2024-05-01T10:30:00.000Z",
    },
    {
        id: "2",
        title: "Sportverein gewinnt Meisterschaft",
        text: "Nach einer spannenden Saison konnte der lokale Sportverein gestern die Meisterschaft für sich entscheiden...",
        category: "sport",
        createdAt: "2024-05-02T14:15:00.000Z",
    },
];

// Funktion zum Abrufen aller Artikel
export function getAllArticles() {
    return [...articles];
}

// Funktion zum Abrufen eines Artikels anhand der ID
export function getArticleById(id) {
    return articles.find((article) => article.id === id) || null;
}

// Funktion zum Erstellen eines neuen Artikels
export function createArticle(articleData) {
    const newArticle = {
        ...articleData,
        id: String(articles.length + 1), // Einfache ID-Generierung
        createdAt: new Date().toISOString(),
    };

    articles.push(newArticle);
    return newArticle;
}
```

2. Aktualisiere die Route-Handler, um diese Mock-Datenbank zu verwenden:

```jsx
// /app/api/news/route.js
import { NextResponse } from "next/server";
import { z } from "zod";
import { getAllArticles, createArticle } from "@/lib/mock-db";

// Validierungsschema bleibt gleich...

// GET-Handler mit Mock-DB
export async function GET() {
    try {
        // Artikel aus der Mock-Datenbank abrufen
        const articles = getAllArticles();

        return NextResponse.json({ data: articles }, { status: 200 });
    } catch (error) {
        console.error("Fehler beim Abrufen der Nachrichtenartikel:", error);

        return NextResponse.json(
            { error: "Beim Abrufen der Nachrichtenartikel ist ein Fehler aufgetreten." },
            { status: 500 },
        );
    }
}

// POST-Handler mit Mock-DB
export async function POST(request) {
    try {
        const body = await request.json();

        // Validierung durchführen (bleibt gleich)...

        // Validierte Daten an die Mock-DB übergeben
        const newArticle = createArticle(validationResult.data);

        return NextResponse.json(
            {
                message: "Nachrichtenartikel erfolgreich erstellt",
                article: newArticle,
            },
            { status: 201 },
        );
    } catch (error) {
        // Fehlerbehandlung bleibt gleich...
    }
}
```

```jsx
// /app/api/news/[id]/route.js
import { NextResponse } from "next/server";
import { getArticleById } from "@/lib/mock-db";

export async function GET(request, { params }) {
    try {
        const id = params.id;
        const article = getArticleById(id);

        if (!article) {
            return NextResponse.json({ error: "Nachrichtenartikel nicht gefunden" }, { status: 404 });
        }

        return NextResponse.json({ data: article }, { status: 200 });
    } catch (error) {
        console.error("Fehler beim Abrufen des Nachrichtenartikels:", error);

        return NextResponse.json(
            { error: "Beim Abrufen des Nachrichtenartikels ist ein Fehler aufgetreten." },
            { status: 500 },
        );
    }
}
```

## Teilaufgabe 5: Erweiterte Fehlerbehandlung

Implementiere eine verbesserte Fehlerbehandlung für verschiedene Szenarien:

1. Erstelle einen zentralen Fehlerhandler für die API-Routen:

```jsx
// /lib/api-error.js
export class ApiError extends Error {
    constructor(message, statusCode, details = null) {
        super(message);
        this.statusCode = statusCode;
        this.details = details;
    }
}

export function handleApiError(error) {
    console.error("API-Fehler:", error);

    if (error instanceof ApiError) {
        const responseBody = { error: error.message };

        // Füge Details hinzu, falls vorhanden
        if (error.details) {
            responseBody.details = error.details;
        }

        return new Response(JSON.stringify(responseBody), {
            status: error.statusCode,
            headers: { "Content-Type": "application/json" },
        });
    }

    // Standard-Serverfehler
    return new Response(JSON.stringify({ error: "Ein interner Serverfehler ist aufgetreten." }), {
        status: 500,
        headers: { "Content-Type": "application/json" },
    });
}
```

2. Implementiere den Fehlerhandler in Deinen Route-Handlern:

```jsx
// Aktualisierte Version von /app/api/news/route.js
import { NextResponse } from "next/server";
import { z } from "zod";
import { getAllArticles, createArticle } from "@/lib/mock-db";
import { ApiError, handleApiError } from "@/lib/api-error";

// Validierungsschema bleibt gleich...

export async function GET() {
    try {
        const articles = getAllArticles();

        return NextResponse.json({ data: articles }, { status: 200 });
    } catch (error) {
        return handleApiError(error);
    }
}

export async function POST(request) {
    try {
        const body = await request.json().catch(() => {
            throw new ApiError("Ungültiges JSON-Format.", 400);
        });

        // Validierung durchführen
        const validationResult = articleSchema.safeParse(body);

        if (!validationResult.success) {
            const errors = validationResult.error.errors.map((error) => ({
                path: error.path.join("."),
                message: error.message,
            }));

            throw new ApiError("Validierungsfehler", 400, errors);
        }

        const newArticle = createArticle(validationResult.data);

        return NextResponse.json(
            {
                message: "Nachrichtenartikel erfolgreich erstellt",
                article: newArticle,
            },
            { status: 201 },
        );
    } catch (error) {
        return handleApiError(error);
    }
}
```

## Teilaufgabe 6: Verbindung des Formulars mit der API

Jetzt kannst Du das zuvor erstellte Formular mit der API verbinden. Aktualisiere die `onSubmit`-Funktion in der `/app/news/add/page.jsx`-Datei:

```jsx
// Aktualisierte onSubmit-Funktion in /app/news/add/page.jsx

const BASE_URL = "http://localhost:3000";
async function onSubmit(values) {
    setIsSubmitting(true);

    try {
        const response = await fetch(BASE_URL + "/api/news", {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
            },
            body: JSON.stringify(values),
        });

        const data = await response.json();

        if (!response.ok) {
            // Fehlerbehandlung
            if (data.details) {
                // Validierungsfehler verarbeiten
                data.details.forEach((error) => {
                    // Setze Fehler im Formular
                    form.setError(error.path, {
                        type: "server",
                        message: error.message,
                    });
                });

                throw new Error("Bitte überprüfe die Eingabefelder.");
            } else {
                throw new Error(data.error || "Ein Fehler ist aufgetreten.");
            }
        }

        // Erfolgsfall: Nachricht anzeigen und zur News-Übersicht navigieren
        toast.success("Nachrichtenartikel erfolgreich erstellt!");
        router.push("/news");
    } catch (error) {
        console.error("Fehler beim Senden des Formulars:", error);
        toast.error(error.message || "Ein Fehler ist aufgetreten.");
    } finally {
        setIsSubmitting(false);
    }
}
```

## Teilaufgabe 7: Testen der API mit einem API-Client

Um Deine API zu testen, kannst Du einen API-Client wie Postman oder das Thunder Client Extension für Visual Studio Code verwenden. Hier sind einige Testfälle:

1. **GET /api/news**: Sollte alle Nachrichtenartikel zurückgeben
2. **GET /api/news/1**: Sollte einen einzelnen Nachrichtenartikel zurückgeben
3. **GET /api/news/999**: Sollte eine 404-Fehlerantwort zurückgeben
4. **POST /api/news** mit gültigen Daten: Sollte einen neuen Artikel erstellen
5. **POST /api/news** mit ungültigen Daten: Sollte Validierungsfehler zurückgeben

Du kannst auch direkt im Browser testen, indem Du `/api/news` aufrufst, um die GET-Anfrage zu senden.

## Bonusaufgabe: Implementierung zusätzlicher HTTP-Methoden

1. Implementiere die PUT-Methode, um einen vorhandenen Nachrichtenartikel zu aktualisieren:

```jsx
// In /app/api/news/[id]/route.js
import { NextResponse } from "next/server";
import { z } from "zod";
import { getArticleById, updateArticle } from "@/lib/mock-db";
import { ApiError, handleApiError } from "@/lib/api-error";

// GET-Handler bleibt gleich...

// Validierungsschema (wie bei POST, aber alle Felder optional)
const updateArticleSchema = z.object({
    title: z.string().min(5).optional(),
    text: z.string().min(20).optional(),
    category: z.enum(["politik", "wirtschaft", "technologie", "sport", "kultur"]).optional(),
});

export async function PUT(request, { params }) {
    try {
        const id = params.id;

        // Prüfen, ob der Artikel existiert
        const existingArticle = getArticleById(id);
        if (!existingArticle) {
            throw new ApiError("Nachrichtenartikel nicht gefunden", 404);
        }

        // Anfragedaten einlesen
        const body = await request.json().catch(() => {
            throw new ApiError("Ungültiges JSON-Format.", 400);
        });

        // Validierung durchführen
        const validationResult = updateArticleSchema.safeParse(body);

        if (!validationResult.success) {
            const errors = validationResult.error.errors.map((error) => ({
                path: error.path.join("."),
                message: error.message,
            }));

            throw new ApiError("Validierungsfehler", 400, errors);
        }

        // Aktualisieren des Artikels (müsste in mock-db.js implementiert werden)
        const updatedArticle = updateArticle(id, validationResult.data);

        return NextResponse.json(
            {
                message: "Nachrichtenartikel erfolgreich aktualisiert",
                article: updatedArticle,
            },
            { status: 200 },
        );
    } catch (error) {
        return handleApiError(error);
    }
}
```

2. Implementiere die DELETE-Methode, um einen vorhandenen Nachrichtenartikel zu löschen.

## Zusammenfassung

In dieser Übung hast Du:

1. Eine RESTful API mit Next.js Route Handlers implementiert
2. GET- und POST-Methoden für Nachrichtenartikel erstellt
3. Eine Datenvalidierung mit Zod implementiert
4. Eine erweiterte Fehlerbehandlung hinzugefügt
5. Das Frontend-Formular mit der API verbunden
6. Verschiedene Testfälle für die API durchgeführt

Die implementierte API folgt den RESTful-Prinzipien und bietet eine solide Grundlage für die Erweiterung Deines NewsPortal-Projekts. Du kannst die API später leicht an eine echte Datenbank anbinden, indem Du die entsprechenden Funktionen in `mock-db.js` anpasst.

## Hilfreiche Ressourcen

- [Next.js Route Handlers Dokumentation](https://nextjs.org/docs/app/building-your-application/routing/route-handlers)
- [Zod Dokumentation](https://zod.dev/)
- [RESTful API Best Practices](https://restfulapi.net/)
- [HTTP Status Codes](https://developer.mozilla.org/de-DE/docs/Web/HTTP/Status)
