# portfolio-app

Simple portfolio site built with Astro and Tailwind CSS.

## Requirements

- Node.js `22.12.0` or newer
- npm
- Docker (optional, for container build/run)

## Run locally

1. Install dependencies:

```bash
npm ci
```

2. Create your environment file:

```bash
cp .env.example .env
```

3. Update the values in `.env` as needed. The contact form uses SMTP settings from this file.

4. Start the development server:

```bash
npm run dev
```

The app will be available at `http://localhost:4321`.

## Production build

Build the app:

```bash
npm run build
```

Start the built server:

```bash
npm run start
```

## Build Docker image

Build the image from the included `Dockerfile`:

```bash
docker build -t portfolio-app .
```

## Run with Docker

Run the container and pass the same environment file:

```bash
docker run --rm -p 4321:4321 --env-file .env portfolio-app
```

The app will be available at `http://localhost:4321`.
