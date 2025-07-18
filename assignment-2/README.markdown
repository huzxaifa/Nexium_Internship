# Blog Summarizer

A web application that summarizes blog articles from URLs, stores summaries in Supabase, saves full article text in MongoDB, and translates summaries into multiple languages.

## Vercel Deployment
- https://blog-summarizer-frontend.vercel.app/

## Features

- **Summarization**: Extracts and summarizes blog content from a provided URL.
- **Storage**:
  - Summaries are stored in a Supabase table (`summaries`).
  - Full article text is stored in a MongoDB collection (`full_text_articles`).
- **Translation**: Translates summaries into languages like Urdu, French, German, Russian, and Spanish using the MyMemory API.
- **Frontend**: Built with React and Vite for a responsive UI.
- **Backend**: Node.js/Express server handles API requests, database interactions, and article scraping.

## Tech Stack

- **Frontend**: React, Vite, Tailwind CSS, Redux Toolkit
- **Backend**: Node.js, Express
- **Databases**:
  - Supabase (PostgreSQL) for storing summaries
  - MongoDB (Atlas or local) for storing full article text
- **APIs**:
  - MyMemory API for translations
  - External summarization API (via Redux Toolkit)
- **Libraries**: Axios, Cheerio (for HTML parsing)

## Prerequisites

- Node.js (v18 or higher)
- npm (v8 or higher)
- Supabase account and project
- MongoDB instance (Atlas or local)
- MyMemory API key (register at mymemory.translated.net)

## Installation

1. **Clone the Repository**:

   ```bash
   git clone https://github.com/your-username/blog-summarizer.git
   cd blog-summarizer
   ```

2. **Install Frontend Dependencies**:

   ```bash
   npm install
   ```

3. **Install Backend Dependencies**:

   ```bash
   cd server
   npm install
   ```

4. **Set Up Environment Variables**:

   - Create `server/.env` with the following:

     ```env
     SUPABASE_URL=https://<your-supabase-project-id>.supabase.co
     SUPABASE_KEY=<your-supabase-anon-key>
     MONGODB_URI=mongodb+srv://<username>:<password>@<cluster>.mongodb.net/blog_summarizer?retryWrites=true&w=majority
     MYMEMORY_API_KEY=<your-mymemory-api-key>
     ```

   - Replace placeholders with your credentials:

     - Supabase: Get from Supabase dashboard (`Settings > API`).
     - MongoDB: Use Atlas URI or `mongodb://localhost:27017/blog_summarizer` for local.
     - MyMemory: Register at mymemory.translated.net.

5. **Set Up Supabase**:

   - Create a `summaries` table in Supabase:

     ```sql
     CREATE TABLE summaries (
       id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
       url TEXT NOT NULL,
       summary TEXT NOT NULL,
       created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
     );
     ```

   - Disable Row-Level Security (RLS) for testing or add an `anon` insert policy:

     ```sql
     CREATE POLICY "Allow anon insert on summaries" ON public.summaries
     FOR INSERT TO anon WITH CHECK (true);
     ```

6. **Set Up MongoDB**:

   - Ensure the `blog_summarizer` database exists (created automatically on first insert).
   - Verify MongoDB Atlas network access (whitelist your IP or use `0.0.0.0/0` for testing).

## Usage

1. **Run the Backend**:

   ```bash
   cd server
   npm start
   ```

   - Server runs on `http://localhost:3000`.

2. **Run the Frontend**:

   ```bash
   cd ..
   npm run dev
   ```

   - Open `http://localhost:5173` (or Vite’s default port) in your browser.

3. **Summarize and Store**:

   - Enter a blog URL and click "Submit" to generate a summary.
   - Summary is stored in Supabase (`summaries` table).
   - Full article text is stored in MongoDB (`full_text_articles` collection).

4. **Translate**:

   - Select a language (e.g., Urdu) and click "Translate" to translate the summary.

## Project Structure

```
blog-summarizer/
├── server/
│   ├── services/
│   │   ├── mongodb.js       # MongoDB operations (full text storage)
│   │   ├── supabase.js      # Supabase operations (summary storage)
│   │   ├── translate.js     # MyMemory API translation
│   ├── index.js             # Express server and API endpoints
│   ├── .env                # Environment variables
├── src/
│   ├── components/
│   │   ├── Demo.jsx        # Main React component for UI
│   ├── services/
│   │   ├── article.js      # Summarization API (Redux Toolkit)
│   ├── assets/             # Images (link icon, loader, etc.)
├── package.json            # Frontend dependencies and scripts
├── server/package.json     # Backend dependencies and scripts
```

## Notes

- **MongoDB Atlas**: Ensure IP is whitelisted in `Network Access`. Use `readWrite` permissions for the database user.
- **Supabase**: Ensure `anon` key has insert permissions for the `summaries` table if RLS is enabled.
- **Text Extraction**: Full text is extracted using Cheerio. Adjust selectors in `server/index.js` if blog content isn’t captured correctly.

## Troubleshooting

- **Supabase Errors**: Check `SUPABASE_URL`, `SUPABASE_KEY`, and table permissions.
- **MongoDB Errors**: Verify `MONGODB_URI`, IP whitelist, and user permissions.
- **Translation Errors**: Ensure `MYMEMORY_API_KEY` is valid and within quota.
- **Logs**: Enable console logs in `server/services/*.js` for debugging.
