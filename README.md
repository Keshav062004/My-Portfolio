# Keshav Gupta — Portfolio

A personal portfolio presenting selected projects, work experience, education, and certifications.

## Live site

Live: <https://portfoliioo-xi.vercel.app/>

## Screenshots

Add screenshots of the site at these paths:

- `docs/screenshots/home.png`
- `docs/screenshots/certifications.png`

The screenshots are placeholders; add the image files to the repository when available.

## Features

- Project cards with project-specific detail popups.
- Project details include project information, technology, features, architecture, and screenshots.
- FundScope India has its own finance-themed project card and details.
- Work experience and education are presented in animated, scroll-triggered timelines.
- Certifications can be searched and filtered by issuer.
- Certificate PDFs can be viewed in-page, maximized, or minimized.
- Layouts adapt to mobile, tablet, and desktop screen sizes.
- A resume is available from the site header.

## Tech stack

| Area | Technology |
| --- | --- |
| Framework | Vue `^3.5.22` |
| Language | TypeScript `~5.9.3` |
| Animation | GSAP `^3.13.0` with its ScrollTrigger plugin |
| Styling | SCSS (Sass `^1.93.2`) and Vue scoped styles |
| Build tool | Vite `^7.1.7` |

Dependency versions are the ranges declared in `package.json`.

## Project structure

```text
.
├── src/
│   └── features/
│       └── home/
│           └── components/
├── public/
│   ├── certificates/
│   ├── certificate-icons/
│   ├── images/
│   └── project-images/
├── package.json
└── vite.config.ts
```

`src/` contains the Vue application and its feature modules.

`src/features/home/components/` contains the home page sections and their components.

`public/certificates/` holds certificate PDF files.

`public/certificate-icons/` holds issuer and certificate icons.

`public/images/` holds other site images, including experience logos.

`public/project-images/` holds images used in project details.

## Getting started

Use a supported Node.js and npm installation.

Install the project dependencies:

```sh
npm install
```

Start the Vite development server:

```sh
npm run dev
```

Create a production build:

```sh
npm run build
```

The build command first runs the Vue and TypeScript project check, then runs Vite.

## Adding content

### Add a certificate

Add an object to the `certifications` array in
`src/features/certificates/components/CertificatesPage.vue`.

Each certificate entry uses these fields:

- `name`: the certificate title.
- `issuer`: the organization that issued it.
- `date`: the displayed date.
- `icon`: the path to its icon under `public/certificate-icons/`.
- `pdf`: the path to its PDF under `public/certificates/`.

Put the PDF file in `public/certificates/` and use its public path in the `pdf` field.

Add or reuse an issuer icon in `public/certificate-icons/` and set the `icon` field.

### Add a project

Add the project object to the `projects` array in
`src/features/home/components/Projects.vue`.

Project objects define the card content, visual type, technology list, and details
shown by the project popup.

Provide project-specific screenshots in `public/project-images/` and reference
them from the project's `screenshots` entries.

### Replace the resume or personal details

Replace the resume PDF at `public/KeshavGupta_Resume.pdf` and keep the header's
download link aligned with the file name.

The displayed name is defined in `src/features/home/components/BoxDescription.vue`
and `src/features/home/components/BoxDetails.vue`.

Social and contact destinations are defined in `src/content/social.ts`.

## Deployment

The site is deployed on Vercel.

The production build is created by Vite in the `dist/` folder.

The build script is defined in `package.json`; Vite uses `dist/` as its default
build output directory.

## Contact

- Email: <keshavgupta072004@gmail.com>
- LinkedIn: <https://www.linkedin.com/in/keshav07/>
- GitHub: <https://github.com/Keshav062004/>

Replace these placeholders with the contact links you want to publish.

## License

See [`license.md`](./license.md) for the license terms and required attribution.

The original portfolio template attribution is retained in accordance with that
file.
