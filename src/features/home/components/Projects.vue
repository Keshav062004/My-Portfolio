<script setup lang="ts">
import {
  ref,
  computed,
  nextTick,
  onMounted,
  onUnmounted,
} from "vue";

import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import ProjectDetailsModal from "../../projects/components/ProjectDetailsModal.vue";

gsap.registerPlugin(ScrollTrigger);

const emit = defineEmits<{
  loaded: [];
}>();

type Project = {
  id: number;
  category: string;
  type: string;
  title: string;
  subtitle: string;
  description: string;
  url: string;

  technologies: {
    name: string;
    type: string;
  }[];

  accent: "orange" | "blue" | "red";

  visual:
    | "compiler"
    | "health"
    | "security"
    | "finance";
  architecture: {
    nodes: { title: string; subtitle: string; icon: string }[];
    secondary: { label: string; icon: string }[];
  };
  stack: string[];
  github?: string;
  overview: string;
  features: string[];
  highlights: { title: string; text: string }[];
  next: string[];
  whatsNext?: { title: string; description: string }[];
  screenshots: { src: string; label: string; caption: string }[];
};

const projects: Project[] = [
  {
    id: 3,
    category: "Web Applications",
    type: "FULL STACK",
    title: "CompileX",
    subtitle: "Online IDE & Compiler",
    description:
      "Online IDE & Compiler with real-time code execution, AI assistance and collaborative features.",
    url: "https://compile-x-t4w5.vercel.app/",
    github: "https://github.com/Keshav062004",
    technologies: [
      { name: "React", type: "react" },
      { name: "Node", type: "node" },
      { name: "Mongo", type: "mongo" },
      { name: "WebSockets", type: "websocket" },
      { name: "Docker", type: "docker" },
    ],
    accent: "orange",
    visual: "compiler",
    architecture: {
      nodes: [
        { title: "React Client", subtitle: "Frontend", icon: "react" },
        { title: "Node / Express", subtitle: "Application API", icon: "node" },
        { title: "Execution API", subtitle: "Data / Services", icon: "api" },
      ],
      secondary: [
        { label: "Ace Editor", icon: "code" },
        { label: "Remote execution", icon: "play" },
        { label: "stdin / stdout", icon: "input" },
        { label: "Copy / Download", icon: "download" },
      ],
    },
    stack: ["React", "Node.js", "Express.js", "MongoDB", "WebSockets", "Docker", "GitHub Actions"],
    overview:
      "CompileX is a browser-based coding environment built around a familiar editor workflow. The supplied project supports C, C++, Java, Python, Go and JavaScript, with syntax highlighting, live code editing, remote execution, input/output, copy and download actions.",
    features: [
      "Multi-language editor supporting 6 programming languages",
      "Ace-powered editor with syntax highlighting and live editing",
      "Run code remotely and inspect output or execution errors",
      "Copy and download source code directly from the editor",
      "Input panel for programs that require stdin",
    ],
    highlights: [
      { title: "Editor experience", text: "Configured Ace Editor modes for C/C++, Java, Python, Go and JavaScript with active-line highlighting and autocomplete." },
      { title: "Execution flow", text: "The editor packages source code, language and stdin into a request and displays returned output or errors." },
      { title: "Developer utilities", text: "Built-in copy and download actions make it easy to reuse code outside the browser." },
    ],
    next: [
      "Add persistent collaborative sessions and shared project history",
      "Expand the execution service with more languages and sandbox controls",
      "Add richer AI-assisted coding workflows",
    ],
    screenshots: [
    {
      src: "/project-images/compilex_editor.png",
      label: "Code Editor",
      caption: "CompileX collaborative code editor"
    },
    {
      src: "/project-images/compilex_execution.png",
      label: "Execution",
      caption: "CompileX code execution workflow"
    },
    {
      src: "/project-images/compilex_developer_tools.png",
      label: "Developer Tools",
      caption: "CompileX developer tools and workflow"
    }
  ],
  },

  {
    id: 1,
    category: "Healthcare",
    type: "HEALTHCARE",
    title: "MediConnect",
    subtitle: "Healthcare Communication Platform",
    description:
      "Secure healthcare platform for appointment booking, doctors, patients and medical records.",
    url: "https://medi-connect-kg.netlify.app",
    github: "https://github.com/Keshav062004",
    technologies: [
      { name: "React", type: "react" },
      { name: "Node", type: "node" },
      { name: "Prisma", type: "prisma" },
      { name: "PostgreSQL", type: "postgres" },
      { name: "JWT", type: "jwt" },
    ],
    accent: "blue",
    visual: "health",
    architecture: {
      nodes: [
        { title: "React App", subtitle: "Frontend", icon: "react" },
        { title: "Node / Express", subtitle: "Application API", icon: "node" },
        { title: "PostgreSQL", subtitle: "Data / Services", icon: "database" },
      ],
      secondary: [
        { label: "JWT Auth", icon: "lock" },
        { label: "Prisma ORM", icon: "database" },
        { label: "Cloudinary", icon: "cloud" },
        { label: "Role-based dashboards", icon: "layers" },
      ],
    },
    stack: ["React", "Redux Toolkit", "Node.js", "Express.js", "Prisma", "PostgreSQL", "JWT", "Cloudinary"],
    overview:
      "MediConnect is a full-stack healthcare platform with separate patient, doctor and admin workflows. The supplied codebase includes authentication, doctor discovery, appointments, schedules, prescriptions, medicines, reviews, favourites, blogs, contact pages and administrative dashboards.",
    features: [
      "Role-based workflows for patients, doctors and administrators",
      "Doctor discovery, profiles, specialities and time-slot management",
      "Appointment booking, tracking, payment and invoice flows",
      "Prescription, medicine, treatment and patient-management modules",
      "Reviews, favourites, blogs and healthcare information pages",
    ],
    highlights: [
      { title: "Domain modelling", text: "The Prisma schema models patients, doctors, appointments, payments, prescriptions, medicines, reviews, favourites and schedules as related entities." },
      { title: "Authentication", text: "The backend includes JWT-based authentication, password hashing and protected dashboard routes." },
      { title: "Modular API", text: "The Express backend separates controllers, services and routes for appointment, doctor, patient, prescription, medicine and other modules." },
    ],
    next: [
      "Add richer real-time doctor/patient communication",
      "Improve observability, validation and automated API testing",
      "Add a more unified appointment and notification experience",
    ],
    screenshots: [
    {
      src: "/project-images/mediconnect_homepage.png",
      label: "Homepage",
      caption: "MediConnect healthcare platform homepage"
    },
    {
      src: "/project-images/mediconnect_login.png",
      label: "Login",
      caption: "MediConnect authentication interface"
    },
    {
      src: "/project-images/mediconnect_dashboard.png",
      label: "Dashboard",
      caption: "MediConnect healthcare dashboard"
    }
  ],
  },

  {
    id: 4,
    category: "Security",
    type: "AI / SECURITY",
    title: "PhishGuard",
    subtitle: "AI Phishing Detection",
    description:
      "AI-powered phishing detection system that analyzes URLs, content and emails in real-time.",
    url: "https://phishing-webiste-detector.vercel.app/",
    github: "https://github.com/Keshav062004",
    technologies: [
      { name: "Python", type: "python" },
      { name: "Flask", type: "flask" },
      { name: "Scikit-learn", type: "ml" },
      { name: "Tailwind", type: "tailwind" },
    ],
    accent: "red",
    visual: "security",
    architecture: {
      nodes: [
        { title: "Browser", subtitle: "Frontend", icon: "eye" },
        { title: "Flask", subtitle: "Application API", icon: "server" },
        { title: "ML Model", subtitle: "phishing.pkl", icon: "shield" },
      ],
      secondary: [
        { label: "URL cleaning", icon: "scan" },
        { label: "Vectorizer", icon: "layers" },
        { label: "Classifier", icon: "shield" },
        { label: "Flask template", icon: "server" },
      ],
    },
    stack: ["Python", "Flask", "Scikit-learn", "Pandas", "NumPy", "Tailwind CSS"],
    overview:
      "PhishGuard is a Flask web application that takes a website URL, cleans the input, transforms it with a saved vectorizer and sends it to a trained phishing classifier. The application then clearly reports whether the URL is classified as phishing or safe.",
    features: [
      "Simple URL scanning workflow from the browser",
      "Saved ML model and vectorizer loaded by the Flask backend",
      "URL preprocessing before prediction",
      "Clear phishing or safe result states",
      "Lightweight deployment setup with Gunicorn support",
    ],
    highlights: [
      { title: "Prediction pipeline", text: "The Flask route strips the protocol and optional www prefix, transforms the cleaned URL and runs the trained classifier." },
      { title: "Model integration", text: "The supplied project loads phishing.pkl and vectorizer.pkl at application startup and uses them for inference." },
      { title: "User feedback", text: "Prediction results are rendered as distinct warning or success states so the result is immediately understandable." },
    ],
    next: [
      "Add richer URL feature extraction and confidence scoring",
      "Introduce automated model evaluation and retraining workflows",
      "Add a history dashboard for previously scanned URLs",
    ],
    screenshots: [
    {
      src: "/project-images/phishguard_detection.png",
      label: "Detection UI",
      caption: "PhishGuard URL detection interface"
    },
    {
      src: "/project-images/phishguard_phishing_result.png",
      label: "Phishing Result",
      caption: "PhishGuard phishing classification result"
    },
    {
      src: "/project-images/phishguard_scan_report.png",
      label: "Scan Report",
      caption: "PhishGuard scan analysis and result"
    }
  ],
  },

  {
    id: 2,
    category: "Finance",
    type: "FINANCE / ANALYTICS",
    title: "FundScope India",
    subtitle: "Mutual Fund NAV Analytics",
    description:
      "Mutual fund NAV analytics: CAGR, Sharpe, drawdown and more for 16 schemes. Educational use.",
    url: "https://fund-scope-india.vercel.app",
    github: "https://github.com/Keshav062004/FundScope-India",
    technologies: [
      { name: "React", type: "react" },
      { name: "Python", type: "python" },
      { name: "FastAPI", type: "fastapi" },
      { name: "SQL", type: "sql" },
      { name: "Pandas", type: "pandas" },
      { name: "Power BI", type: "powerbi" },
    ],
    accent: "orange",
    visual: "finance",
    architecture: {
      nodes: [
        { title: "Browser", subtitle: "React frontend", icon: "react" },
        { title: "FastAPI", subtitle: "Application API", icon: "api" },
        { title: "SQLite", subtitle: "NAV data + metrics", icon: "database" },
      ],
      secondary: [
        { label: "Firebase Auth", icon: "lock" },
        { label: "Google Gemini", icon: "star" },
        { label: "Pandas metrics", icon: "database" },
        { label: "Render backend", icon: "server" },
      ],
    },
    stack: ["React", "Python", "FastAPI", "SQL", "Pandas", "Power BI"],
    overview:
      "FundScope India presents mutual fund NAV history and analytics for 16 schemes, including CAGR, Sharpe ratio, and drawdown metrics. It is an educational analytics project, not investment advice.",
    features: [
      "Explore NAV history across 16 mutual fund schemes",
      "Compare CAGR, Sharpe ratio, and drawdown metrics",
      "Review historical data spanning 2013–2026",
      "Use an interactive dashboard for educational analysis",
    ],
    highlights: [
      { title: "NAV analytics", text: "Historical NAV observations power scheme comparisons and return analysis." },
      { title: "Risk metrics", text: "CAGR, Sharpe ratio, drawdown, and additional risk measures summarize scheme performance." },
      { title: "Data workflow", text: "A React interface connects to a FastAPI application backed by SQLite and Pandas metrics." },
    ],
    next: [],
    whatsNext: [
      {
        title: "Always-on hosting with a custom domain",
        description:
          "Move the backend off the free tier so it no longer sleeps, and connect a custom domain for cleaner branding and a more reliable login flow.",
      },
      {
        title: "Automated daily data refresh",
        description:
          "Schedule the NAV pipeline to run on its own and log each run's row counts and validation results, so the dataset stays current without manual updates.",
      },
      {
        title: "Broader coverage and comparison tools",
        description:
          "Add more schemes and benchmark indices, plus side-by-side comparisons over matched periods, so users can evaluate funds against the same market conditions.",
      },
    ],
    screenshots: [
      {
        src: "/project-images/FundScope_India_landingpage.png",
        label: "Landing page",
        caption: "FundScope India landing page",
      },
      {
        src: "/project-images/FundScope_India_dashboard.png",
        label: "NAV dashboard",
        caption: "Mutual fund NAV analytics dashboard",
      },
      {
        src: "/project-images/FundScope_India_loginpage.png",
        label: "Login page",
        caption: "FundScope India sign-in experience",
      },
    ],
  },
];

projects.sort((a, b) => a.id - b.id);

const activeIndex = ref(0);
const detailsProject = ref<Project | null>(null);

const financeCounts = ref({
  schemes: 0,
  navRows: 0,
  historyStart: 0,
  historyEnd: 0,
  riskMetrics: 0,
});

const FINANCE_COUNT_DURATION = 1500;
const FINANCE_COUNT_CYCLE = FINANCE_COUNT_DURATION + 2000;

let financeCountFrame = 0;
let financeCountStart: number | null = null;
let financeMotionQuery: MediaQueryList | null = null;

const updateFinanceCounts = (progress: number) => {
  financeCounts.value = {
    schemes: Math.round(16 * progress),
    navRows: Math.round(42 * progress),
    historyStart: Math.round(2013 * progress),
    historyEnd: Math.round(26 * progress),
    riskMetrics: Math.round(6 * progress),
  };
};

const showFinalFinanceCounts = () => {
  financeCounts.value = {
    schemes: 16,
    navRows: 42,
    historyStart: 2013,
    historyEnd: 26,
    riskMetrics: 6,
  };
};

const animateFinanceCounts = (timestamp: number) => {
  if (financeCountStart === null) financeCountStart = timestamp;

  const elapsed = (timestamp - financeCountStart) % FINANCE_COUNT_CYCLE;
  updateFinanceCounts(Math.min(elapsed / FINANCE_COUNT_DURATION, 1));
  financeCountFrame = requestAnimationFrame(animateFinanceCounts);
};

const handleFinanceMotionPreference = (event: MediaQueryListEvent) => {
  cancelAnimationFrame(financeCountFrame);
  financeCountStart = null;

  if (event.matches) {
    showFinalFinanceCounts();
    return;
  }

  financeCountFrame = requestAnimationFrame(animateFinanceCounts);
};

const startFinanceCountAnimation = () => {
  if (typeof window === "undefined") return;

  financeMotionQuery = window.matchMedia("(prefers-reduced-motion: reduce)");
  financeMotionQuery.addEventListener("change", handleFinanceMotionPreference);

  if (financeMotionQuery.matches) {
    showFinalFinanceCounts();
    return;
  }

  financeCountFrame = requestAnimationFrame(animateFinanceCounts);
};

/* =========================================================
   PROJECT TECHNOLOGY ICONS
   Inline SVG keeps the cards self-contained — no icon package
   or external CDN is required.
========================================================= */

const techIconSvg = (type: string): string => {
  const icons: Record<string, string> = {
    react: `<svg viewBox="0 0 24 24" aria-hidden="true"><circle cx="12" cy="12" r="2.2" fill="currentColor"/><ellipse cx="12" cy="12" rx="9.2" ry="3.6" fill="none" stroke="currentColor" stroke-width="1.6"/><ellipse cx="12" cy="12" rx="9.2" ry="3.6" fill="none" stroke="currentColor" stroke-width="1.6" transform="rotate(60 12 12)"/><ellipse cx="12" cy="12" rx="9.2" ry="3.6" fill="none" stroke="currentColor" stroke-width="1.6" transform="rotate(120 12 12)"/></svg>`,
    node: `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M12 2.8 20 7.4v9.2L12 21.2 4 16.6V7.4L12 2.8Z" fill="currentColor"/><text x="12" y="14.4" text-anchor="middle" font-size="6.5" font-weight="800" fill="white">JS</text></svg>`,
    mongo: `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M12.3 2.7c-.6 3.1-2.9 4.9-3.7 7.7-1 3.4.3 6.6 3.4 9.1l.3 2.1h1.2l.2-2.1c2.1-2.3 2.8-5 2-7.6-.7-2.8-2.8-5.1-3.4-9.2Z" fill="currentColor"/><path d="M12.6 19.5c.2-4.4.2-7.7-.2-11.1" fill="none" stroke="white" stroke-width=".9" stroke-linecap="round"/></svg>`,
    websocket: `<svg viewBox="0 0 24 24" aria-hidden="true"><circle cx="5" cy="12" r="2.2" fill="currentColor"/><circle cx="19" cy="6" r="2.2" fill="currentColor"/><circle cx="19" cy="18" r="2.2" fill="currentColor"/><path d="M7 11l9.8-4M7 13l9.8 4M19 8.2v7.6" fill="none" stroke="currentColor" stroke-width="1.7" stroke-linecap="round"/></svg>`,
    docker: `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M3.2 14.5h17.6c-.8 3.5-3.4 5.4-7.4 5.4H10c-3.7 0-5.8-1.8-6.8-5.4Z" fill="currentColor"/><path d="M5.4 11.2h2.1V9h2.1v2.2h2.1V9h2.1v2.2H16V9h2.1v2.2c1.1-.2 2.1.1 2.8.8-1.3.5-2.2 1.1-2.8 1.9H5.4v-2.7Z" fill="currentColor"/></svg>`,
    github: `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M12 2.8a9.3 9.3 0 0 0-2.9 18.1c.5.1.7-.2.7-.5v-1.8c-2.9.6-3.5-1.2-3.5-1.2-.5-1.2-1.1-1.5-1.1-1.5-.9-.6.1-.6.1-.6 1 .1 1.6 1 1.6 1 .9 1.6 2.4 1.1 3 .8.1-.7.4-1.1.6-1.4-2.3-.3-4.7-1.1-4.7-5 0-1.1.4-2 .9-2.7-.1-.2-.4-1.3.1-2.7 0 0 .8-.3 2.8 1a9.6 9.6 0 0 1 5 0c1.9-1.3 2.8-1 2.8-1 .5 1.4.2 2.5.1 2.7.6.7.9 1.6.9 2.7 0 3.9-2.4 4.7-4.7 5 .4.3.7 1 .7 1.9v2.8c0 .3.2.6.7.5A9.3 9.3 0 0 0 12 2.8Z" fill="currentColor"/></svg>`,
    python: `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M12 3.2c-3.6 0-3.4 1.6-3.4 1.6v2.3H12v.7H7.3c-2.2 0-3.3 1.3-3.3 3.7s1 3.6 3.3 3.6h1.3v-2.1c0-1.7 1.2-3 3-3h3.8c1.8 0 3.1-1.2 3.1-3V6.2c0-1.8-1.6-3-3.4-3H12Z" fill="currentColor"/><path d="M12 20.8c3.6 0 3.4-1.6 3.4-1.6v-2.3H12v-.7h4.7c2.2 0 3.3-1.3 3.3-3.7s-1-3.6-3.3-3.6h-1.3V11c0 1.7-1.2 3-3 3H8.6c-1.8 0-3.1 1.2-3.1 3v.8c0 1.8 1.6 3 3.4 3H12Z" fill="currentColor" opacity=".62"/></svg>`,
    fastapi: `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M13.5 2.8 5 13h5l-.8 8.2L19 10.6h-5.3l-.2-7.8Z" fill="currentColor"/></svg>`,
    sql: `<svg viewBox="0 0 24 24" aria-hidden="true"><ellipse cx="12" cy="5.5" rx="7.5" ry="3" fill="none" stroke="currentColor" stroke-width="1.8"/><path d="M4.5 5.5v13c0 1.7 3.4 3 7.5 3s7.5-1.3 7.5-3v-13M4.5 10c0 1.7 3.4 3 7.5 3s7.5-1.3 7.5-3M4.5 14.5c0 1.7 3.4 3 7.5 3s7.5-1.3 7.5-3" fill="none" stroke="currentColor" stroke-width="1.6"/></svg>`,
    pandas: `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M7 3v6m0 4v8M17 3v2m0 4v12M4.5 9h5m5 0h5" fill="none" stroke="currentColor" stroke-width="2.4" stroke-linecap="round"/><circle cx="7" cy="11" r="2" fill="currentColor"/><circle cx="17" cy="7" r="2" fill="currentColor"/></svg>`,
    powerbi: `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M4 13h4v8H4zM10 8h4v13h-4zM16 3h4v18h-4z" fill="currentColor"/></svg>`,
    sqlite: `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M4 7c0-1.7 3.6-3 8-3s8 1.3 8 3v10c0 1.7-3.6 3-8 3s-8-1.3-8-3V7Zm0 0c0 1.7 3.6 3 8 3s8-1.3 8-3m-16 5c0 1.7 3.6 3 8 3s8-1.3 8-3" fill="none" stroke="currentColor" stroke-width="1.7"/><path d="m16 3 3-1-1.2 3.3" fill="none" stroke="currentColor" stroke-width="1.5"/></svg>`,
    firebase: `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="m5 17 2.7-12a.8.8 0 0 1 1.5-.2l2 3.3 2-4.1a.8.8 0 0 1 1.5.1L19 17l-7 4-7-4Z" fill="currentColor"/><path d="m5 17 6.2-9 1.5 7.1L19 17" fill="none" stroke="white" stroke-width="1.1" stroke-linejoin="round"/></svg>`,
    gemini: `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M12 2.5c1.1 5.4 4.1 8.4 9.5 9.5-5.4 1.1-8.4 4.1-9.5 9.5-1.1-5.4-4.1-8.4-9.5-9.5 5.4-1.1 8.4-4.1 9.5-9.5Z" fill="currentColor"/></svg>`,
    flask: `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M9 3h6M10 3v6.2L5.5 17a2.5 2.5 0 0 0 2.1 3.8h8.8a2.5 2.5 0 0 0 2.1-3.8L14 9.2V3" fill="none" stroke="currentColor" stroke-width="1.7" stroke-linecap="round" stroke-linejoin="round"/><path d="M7.1 16h9.8" stroke="currentColor" stroke-width="1.7" stroke-linecap="round"/></svg>`,
    ml: `<svg viewBox="0 0 24 24" aria-hidden="true"><circle cx="6" cy="12" r="2" fill="currentColor"/><circle cx="12" cy="6" r="2" fill="currentColor"/><circle cx="18" cy="12" r="2" fill="currentColor"/><circle cx="12" cy="18" r="2" fill="currentColor"/><path d="M7.7 10.5 10.3 7.5M13.7 7.5l2.6 3M16.3 13.5l-2.6 3M10.3 16.5l-2.6-3" fill="none" stroke="currentColor" stroke-width="1.4" stroke-linecap="round"/></svg>`,
    tailwind: `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M4 12c1.3-3.4 3.7-5.1 7.1-5.1 2.2 0 3.8 1.1 4.9 3.2 1.1 2 2.4 3 4.1 3-1.3 3.4-3.7 5.1-7.1 5.1-2.2 0-3.8-1.1-4.9-3.2C7 13 5.7 12 4 12Z" fill="currentColor"/></svg>`,
    prisma: `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="m4.3 16.9 6.4-12.5c.3-.6 1.1-.6 1.5 0l7.5 13.1c.4.7-.1 1.5-.9 1.5H5.1c-.8 0-1.2-1.1-.8-2.1Z" fill="currentColor"/><path d="m12 8.2-2.2 8.2 5.9-1.3" fill="none" stroke="white" stroke-width="1.2" stroke-linecap="round" stroke-linejoin="round"/></svg>`,
    postgres: `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M7.1 18.7c-.8-2.5-.9-6.2-.2-9.1.8-3.3 3.1-5.1 6.1-5.1 3.4 0 5.3 2.1 4.9 5.3-.3 2.6-1.9 4.1-4.3 4.4-.9.1-1.5.1-2.1.1-.2 1.7-.8 3.1-2.1 4.5" fill="none" stroke="currentColor" stroke-width="1.7" stroke-linecap="round"/><path d="M15.3 13.9c1.2-.5 2.6-1.5 3.7-2.8M9.5 9.1c1.1.8 2.9 1.1 4.7.8" fill="none" stroke="currentColor" stroke-width="1.4" stroke-linecap="round"/></svg>`,
    jwt: `<svg viewBox="0 0 24 24" aria-hidden="true"><rect x="5" y="10" width="14" height="10" rx="2" fill="none" stroke="currentColor" stroke-width="1.7"/><path d="M8 10V7.8a4 4 0 0 1 8 0V10M12 14v2.4" fill="none" stroke="currentColor" stroke-width="1.7" stroke-linecap="round"/></svg>`,
  };

  return icons[type] || `<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M5 7h14M5 12h14M5 17h14" fill="none" stroke="currentColor" stroke-width="1.7" stroke-linecap="round"/></svg>`;
};

const openDetails = (project: Project) => {
  detailsProject.value = project;
};

const closeDetails = () => {
  detailsProject.value = null;
};

const previousIndex = computed(
  () =>
    (activeIndex.value - 1 + projects.length) %
    projects.length
);

const nextIndex = computed(
  () =>
    (activeIndex.value + 1) %
    projects.length
);

const getCardClass = (index: number) => {
  if (index === activeIndex.value) {
    return "is-active";
  }

  if (index === previousIndex.value) {
    return "is-left";
  }

  if (index === nextIndex.value) {
    return "is-right";
  }

  return "is-hidden";
};

const sectionRef = ref<HTMLElement | null>(null);
const headingRef = ref<HTMLElement | null>(null);
const introRef = ref<HTMLElement | null>(null);
const showcaseRef = ref<HTMLElement | null>(null);
const platformRef = ref<HTMLElement | null>(null);

let ctx: gsap.Context | null = null;

const animateCards = () => {
  nextTick(() => {
    const cards =
      showcaseRef.value?.querySelectorAll(
        ".project-slide"
      );

    if (!cards?.length) return;

    cards.forEach((element, index) => {
      const card = element as HTMLElement;
      const state = getCardClass(index);

      gsap.killTweensOf(card);

      if (state === "is-active") {
        gsap.set(card, {
          x: 0,
          y: 0,
          z: 0,
          scale: 1,
          rotateY: 0,
          rotateX: 0,
          rotateZ: 0,
          opacity: 1,
          zIndex: 20,
          transformPerspective: 1400,
        });

        gsap.fromTo(
          card,
          {
            z: -90,
            rotateY: 10,
            rotateX: -4,
            scale: 0.9,
            opacity: 0.55,
          },
          {
            z: 0,
            rotateY: 0,
            rotateX: 0,
            scale: 1,
            opacity: 1,
            duration: 0.85,
            ease: "expo.out",
            overwrite: true,
          }
        );

        gsap.to(card, {
          y: -6,
          duration: 2.8,
          repeat: -1,
          yoyo: true,
          ease: "sine.inOut",
          delay: 0.85,
          overwrite: false,
        });
      }

      if (state === "is-left") {
        gsap.to(card, {
          x: "-54%",
          y: 34,
          scale: 0.72,
          rotateY: 12,
          rotateZ: -1,
          opacity: 0.34,
          zIndex: 5,
          duration: 0.75,
          ease: "power4.out",
          overwrite: true,
        });
      }

      if (state === "is-right") {
        gsap.to(card, {
          x: "54%",
          y: 34,
          scale: 0.72,
          rotateY: -12,
          rotateZ: 1,
          opacity: 0.34,
          zIndex: 5,
          duration: 0.75,
          ease: "power4.out",
          overwrite: true,
        });
      }

      if (state === "is-hidden") {
        gsap.to(card, {
          x: 0,
          y: 80,
          scale: 0.6,
          rotateY: 0,
          opacity: 0,
          zIndex: 0,
          duration: 0.55,
          ease: "power3.inOut",
          overwrite: true,
        });
      }
    });
  });
};

const goNext = () => {
  activeIndex.value =
    (activeIndex.value + 1) %
    projects.length;

  animateCards();
};

const goPrevious = () => {
  activeIndex.value =
    (activeIndex.value - 1 + projects.length) %
    projects.length;

  animateCards();
};

const goToProject = (index: number) => {
  activeIndex.value = index;
  animateCards();
};

const handleMouseMove = (
  event: MouseEvent,
  card: HTMLElement
) => {
  if (!card.classList.contains("is-active")) {
    return;
  }

  const rect = card.getBoundingClientRect();

  const x =
    (event.clientX - rect.left) /
    rect.width;

  const y =
    (event.clientY - rect.top) /
    rect.height;

  const rotateY = (x - 0.5) * 7;
  const rotateX = (y - 0.5) * -7;

  gsap.to(card, {
    rotateY,
    rotateX,
    duration: 0.45,
    ease: "power3.out",
    overwrite: true,
  });
};

const handleMouseLeave = (
  card: HTMLElement
) => {
  if (!card.classList.contains("is-active")) {
    return;
  }

  gsap.to(card, {
    rotateY: 0,
    rotateX: 0,
    duration: 0.45,
    ease: "power3.out",
    overwrite: true,
  });
};

const handleSceneMouseMove = (
  event: MouseEvent
) => {
  const target = event.target as HTMLElement | null;

  // Keep the navigation arrows completely stable and clickable.
  if (target?.closest(".carousel-arrow")) {
    return;
  }

  const showcase = showcaseRef.value;

  if (!showcase) return;

  const rect = showcase.getBoundingClientRect();

  const x =
    (event.clientX - rect.left) /
      rect.width -
    0.5;

  const y =
    (event.clientY - rect.top) /
      rect.height -
    0.5;

  const particles =
    showcase.querySelector(
      ".project-particles"
    );

  const platform =
    showcase.querySelector(
      ".platform-system"
    );

  const cube =
    sectionRef.value?.querySelector(
      ".floating-cube"
    );

  // Do not rotate the complete showcase.
  // Only the decorative 3D layers respond to the mouse.
  if (particles) {
    gsap.to(particles, {
      x: x * 26,
      y: y * 20,
      rotateY: x * 10,
      rotateX: y * -7,
      duration: 1,
      ease: "power3.out",
      overwrite: true,
    });
  }

  if (platform) {
    gsap.to(platform, {
      x: x * 20,
      y: y * 6,
      duration: 1.1,
      ease: "power3.out",
      overwrite: true,
    });
  }

  if (cube) {
    gsap.to(cube, {
      x: x * 18,
      y: y * 14,
      rotateZ: x * 4,
      duration: 1.2,
      ease: "power3.out",
      overwrite: true,
    });
  }
};

const handleSceneMouseLeave = () => {
  const showcase = showcaseRef.value;

  if (!showcase) return;

  const particles =
    showcase.querySelector(
      ".project-particles"
    );

  const platform =
    showcase.querySelector(
      ".platform-system"
    );

  const cube =
    sectionRef.value?.querySelector(
      ".floating-cube"
    );

  if (particles) {
    gsap.to(particles, {
      x: 0,
      y: 0,
      rotateX: 0,
      rotateY: 0,
      duration: 1.2,
      ease: "power4.out",
      overwrite: true,
    });
  }

  if (platform) {
    gsap.to(platform, {
      x: 0,
      y: 0,
      duration: 1.2,
      ease: "power4.out",
      overwrite: true,
    });
  }

  if (cube) {
    gsap.to(cube, {
      x: 0,
      y: 0,
      rotateZ: 0,
      duration: 1.2,
      ease: "power4.out",
      overwrite: true,
    });
  }
};

const handleKeydown = (
  event: KeyboardEvent
) => {
  if (event.key === "ArrowRight") {
    goNext();
  }

  if (event.key === "ArrowLeft") {
    goPrevious();
  }
};

const animateCube = () => {
  const cube =
    sectionRef.value?.querySelector(
      ".cube"
    );

  const wrapper =
    sectionRef.value?.querySelector(
      ".floating-cube"
    );

  if (!cube || !wrapper) return;

  gsap.to(cube, {
    rotateY: "+=360",
    duration: 12,
    repeat: -1,
    ease: "none",
  });

  gsap.to(wrapper, {
    y: -8,
    duration: 2.8,
    repeat: -1,
    yoyo: true,
    ease: "sine.inOut",
  });
};

const animatePlatform = () => {
  if (!platformRef.value) return;

  const rings =
    platformRef.value.querySelectorAll(
      ".platform-ring"
    );

  rings.forEach((ring, index) => {
    gsap.to(ring, {
      scale: index === 0 ? 1.05 : 1.02,
      opacity: index === 0 ? 0.7 : 0.35,
      duration: 2.3 + index * 0.3,
      repeat: -1,
      yoyo: true,
      ease: "sine.inOut",
      delay: index * 0.12,
    });
  });

  const platformTop =
    platformRef.value.querySelector(
      ".platform-top"
    );

  if (platformTop) {
    gsap.to(platformTop, {
      y: -3,
      duration: 2.5,
      repeat: -1,
      yoyo: true,
      ease: "sine.inOut",
    });
  }
};

const animateVisuals = () => {
  const lines =
    sectionRef.value?.querySelectorAll(
      ".code-lines i"
    );

  lines?.forEach((line, index) => {
    gsap.fromTo(
      line,
      {
        scaleX: 0.2,
        transformOrigin: "left center",
      },
      {
        scaleX: 1,
        duration: 0.8 + index * 0.07,
        delay: index * 0.07,
        repeat: -1,
        repeatDelay: 2,
        ease: "power2.out",
      }
    );
  });

  const dots =
    sectionRef.value?.querySelectorAll(
      ".threat-dot"
    );

  dots?.forEach((dot, index) => {
    gsap.to(dot, {
      opacity: 0.25,
      scale: 0.7,
      duration: 1.2 + index * 0.2,
      repeat: -1,
      yoyo: true,
      delay: index * 0.25,
      ease: "sine.inOut",
    });
  });

  const bars =
    sectionRef.value?.querySelectorAll(
      ".health-bar"
    );

  bars?.forEach((bar, index) => {
    gsap.to(bar, {
      scaleY:
        0.65 + (index % 3) * 0.12,
      transformOrigin:
        "bottom center",
      duration:
        1.4 + index * 0.12,
      repeat: -1,
      yoyo: true,
      ease: "sine.inOut",
    });
  });
};

onMounted(async () => {
  startFinanceCountAnimation();
  emit("loaded");

  await nextTick();

  const section = sectionRef.value;

  if (!section) return;

  ctx = gsap.context(() => {
    const heading = headingRef.value;
    const intro = introRef.value;
    const showcase = showcaseRef.value;

    const featureList =
      intro?.querySelector(
        ".feature-list"
      );

    const featureItems =
      featureList?.querySelectorAll(
        ".feature-item"
      );

    const viewAll =
      intro?.querySelector(
        ".view-all-button"
      );

    const cubeWrapper =
      section.querySelector(
        ".floating-cube"
      );

    // Everything starts hidden so the section can reveal itself
    // naturally as the user scrolls into it.
    gsap.set(heading, {
      opacity: 0,
      x: -90,
      rotateY: -10,
      filter: "blur(10px)",
      transformPerspective: 1400,
    });

    gsap.set(intro, {
      opacity: 0,
      x: -100,
      y: 25,
    });

    gsap.set(showcase, {
      opacity: 0,
      x: 100,
      y: -150,
      rotateX: -20,
      rotateY: 10,
      scale: 0.82,
      transformPerspective: 1400,
    });

    if (featureItems?.length) {
      gsap.set(featureItems, {
        opacity: 0,
        x: -70,
      });
    }

    if (viewAll) {
      gsap.set(viewAll, {
        opacity: 0,
        x: -45,
        y: 15,
      });
    }

    if (cubeWrapper) {
      gsap.set(cubeWrapper, {
        opacity: 0,
        x: 120,
        y: -90,
        rotateY: 35,
        scale: 0.65,
      });
    }

    const timeline =
      gsap.timeline({
        scrollTrigger: {
          trigger: section,
          start: "top 88%",
          end: "top 35%",
          scrub: 1.15,
          invalidateOnRefresh: true,
        },
      });

    // Heading comes from the left.
    timeline.to(heading, {
      opacity: 1,
      x: 0,
      rotateY: 0,
      filter: "blur(0px)",
      duration: 0.85,
      ease: "expo.out",
    });

    // Main 3D project showcase drops in from above.
    timeline.to(
      showcase,
      {
        opacity: 1,
        x: 0,
        y: 0,
        rotateX: 0,
        rotateY: 0,
        scale: 1,
        duration: 1.05,
        ease: "expo.out",
      },
      "-=0.62"
    );

    // Supporting copy follows from the left.
    timeline.to(
      intro,
      {
        opacity: 1,
        x: 0,
        y: 0,
        duration: 0.7,
        ease: "power4.out",
      },
      "-=0.72"
    );

    if (featureItems?.length) {
      timeline.to(
        featureItems,
        {
          opacity: 1,
          x: 0,
          rotateY: 0,
          duration: 0.55,
          stagger: 0.1,
          ease: "power3.out",
        },
        "-=0.42"
      );
    }

    if (viewAll) {
      timeline.to(
        viewAll,
        {
          opacity: 1,
          x: 0,
          y: 0,
          duration: 0.5,
          ease: "back.out(1.4)",
        },
        "-=0.28"
      );
    }

    if (cubeWrapper) {
      timeline.to(
        cubeWrapper,
        {
          opacity: 1,
          x: 0,
          y: 0,
          rotateY: 0,
          scale: 1,
          duration: 0.85,
          ease: "back.out(1.25)",
        },
        "-=0.72"
      );
    }

    requestAnimationFrame(() => {
      animateCards();
      animateCube();
      animatePlatform();
      animateVisuals();
      ScrollTrigger.refresh();
    });

    const cards =
      showcase?.querySelectorAll(
        ".project-slide"
      );

    cards?.forEach((element) => {
      const card =
        element as HTMLElement;

      const move = (event: MouseEvent) =>
        handleMouseMove(event, card);

      const leave = () =>
        handleMouseLeave(card);

      card.addEventListener(
        "mousemove",
        move
      );

      card.addEventListener(
        "mouseleave",
        leave
      );

      (
        card as HTMLElement & {
          __cleanup?: () => void;
        }
      ).__cleanup = () => {
        card.removeEventListener(
          "mousemove",
          move
        );

        card.removeEventListener(
          "mouseleave",
          leave
        );
      };
    });

    showcase?.addEventListener(
      "mousemove",
      handleSceneMouseMove
    );

    showcase?.addEventListener(
      "mouseleave",
      handleSceneMouseLeave
    );

    window.addEventListener(
      "keydown",
      handleKeydown
    );
  }, section);
});

onUnmounted(() => {
  cancelAnimationFrame(financeCountFrame);
  financeMotionQuery?.removeEventListener("change", handleFinanceMotionPreference);

  const cards =
    showcaseRef.value?.querySelectorAll(
      ".project-slide"
    );

  cards?.forEach((element) => {
    const card =
      element as HTMLElement & {
        __cleanup?: () => void;
      };

    card.__cleanup?.();
  });

  showcaseRef.value?.removeEventListener(
    "mousemove",
    handleSceneMouseMove
  );

  showcaseRef.value?.removeEventListener(
    "mouseleave",
    handleSceneMouseLeave
  );

  window.removeEventListener(
    "keydown",
    handleKeydown
  );

  ctx?.revert();
  ctx = null;
});
</script>

<template>
  <section
    ref="sectionRef"
    id="projects"
    class="projects"
  >
    <div
      class="projects-background"
      aria-hidden="true"
    >
      <div class="background-glow"></div>
      <div class="background-grid"></div>
      <div class="background-noise"></div>
    </div>

    <div
      class="floating-cube"
      aria-hidden="true"
    >
      <div class="cube">
        <div class="cube-face cube-front"></div>
        <div class="cube-face cube-back"></div>
        <div class="cube-face cube-left"></div>
        <div class="cube-face cube-right"></div>
        <div class="cube-face cube-top"></div>
        <div class="cube-face cube-bottom"></div>
      </div>

      <div class="cube-orbit orbit-one"></div>
      <div class="cube-orbit orbit-two"></div>

      <span class="cube-dot dot-one"></span>
      <span class="cube-dot dot-two"></span>
      <span class="cube-dot dot-three"></span>
    </div>

    <div class="projects-wrapper">

      <aside
        ref="introRef"
        class="projects-intro"
      >
        <div
          ref="headingRef"
          class="projects-heading"
        >
          <div class="projects-eyebrow">
            MY WORK
          </div>

          <h2>
            Projects<span>.</span>
          </h2>

          <div class="heading-line">
            <span></span>
          </div>
        </div>

        <p class="intro-description">
          Real-world solutions I built
          with passion, solving problems
          and delivering impact through
          code and creativity.
        </p>

        <div class="feature-list">

          <div class="feature-item">
            <div class="feature-icon">
              <svg
                viewBox="0 0 24 24"
                fill="none"
              >
                <path
                  d="M13 2 4 14h7l-1 8 9-12h-7l1-8Z"
                  stroke="currentColor"
                  stroke-width="1.8"
                  stroke-linejoin="round"
                />
              </svg>
            </div>

            <div>
              <strong>
                Built for performance
              </strong>

              <span>
                Optimized, scalable &
                efficient
              </span>
            </div>
          </div>

          <div class="feature-item">
            <div class="feature-icon">
              <svg
                viewBox="0 0 24 24"
                fill="none"
              >
                <path
                  d="M12 3 20 6v5c0 5-3.4 8.4-8 10-4.6-1.6-8-5-8-10V6l8-3Z"
                  stroke="currentColor"
                  stroke-width="1.7"
                />

                <path
                  d="m9 12 2 2 4-4"
                  stroke="currentColor"
                  stroke-width="1.7"
                />
              </svg>
            </div>

            <div>
              <strong>
                Secure by design
              </strong>

              <span>
                Security & best practices
                every step of the way
              </span>
            </div>
          </div>

          <div class="feature-item">
            <div class="feature-icon">
              <svg
                viewBox="0 0 24 24"
                fill="none"
              >
                <circle
                  cx="9"
                  cy="7"
                  r="3.5"
                  stroke="currentColor"
                  stroke-width="1.7"
                />

                <path
                  d="M3 20v-1.5a4 4 0 0 1 4-4h5a4 4 0 0 1 4 4V20"
                  stroke="currentColor"
                  stroke-width="1.7"
                />
              </svg>
            </div>

            <div>
              <strong>
                User focused
              </strong>

              <span>
                Crafted with real users
                and real needs in mind
              </span>
            </div>
          </div>

          <div class="feature-item">
            <div class="feature-icon">
              <svg
                viewBox="0 0 24 24"
                fill="none"
              >
                <path
                  d="M7 18a4 4 0 1 1 .6-7.95A5.5 5.5 0 0 1 18 11a3.5 3.5 0 0 1 0 7H7Z"
                  stroke="currentColor"
                  stroke-width="1.7"
                />
              </svg>
            </div>

            <div>
              <strong>
                Cloud ready
              </strong>

              <span>
                Deployed on AWS for
                reliability & speed
              </span>
            </div>
          </div>

        </div>

        <a
  class="view-all-button"
  href="https://github.com/Keshav062004"
  target="_blank"
  rel="noopener noreferrer"
>
  <span>
    View more on GitHub
  </span>

  <span class="button-arrow">
    ↗
  </span>
</a>
      </aside>

      <main
        ref="showcaseRef"
        class="projects-showcase"
      >

        <div class="carousel-area">

          <!-- 3D PARTICLE FIELD -->
          <div
            class="project-particles"
            aria-hidden="true"
          >
            <span
              v-for="n in 28"
              :key="n"
              class="project-particle"
              :style="{
                '--i': n,
                '--x': `${(n * 37) % 100}%`,
                '--y': `${(n * 61) % 100}%`,
                '--z': `${((n * 17) % 80) - 40}px`,
                '--delay': `${(n % 7) * 0.4}s`,
                '--size': `${2 + (n % 3)}px`
              }"
            ></span>
          </div>

          <div
            ref="platformRef"
            class="platform-system"
            aria-hidden="true"
          >
            <div
              class="platform-ring ring-one"
            ></div>

            <div
              class="platform-ring ring-two"
            ></div>

            <div
              class="platform-ring ring-three"
            ></div>

            <div
              class="platform-ring ring-four"
            ></div>

            <div class="platform-base">
              <div class="platform-top"></div>
              <div class="platform-bottom"></div>
            </div>
          </div>

          <button
            class="carousel-arrow carousel-arrow-left"
            type="button"
            aria-label="Previous project"
            @click.stop="goPrevious"
            @mousedown.stop
          >
            <span>‹</span>
          </button>

          <div class="project-carousel">

            <article
              v-for="(
                project,
                index
              ) in projects"
              :key="project.id"
              class="project-slide"
              :class="[
                `project-${project.accent}`,
                getCardClass(index),
              ]"
              :inert="index !== activeIndex"
              :aria-hidden="index !== activeIndex"
            >

              <div class="project-card">

                <div class="project-visual">

                  <div class="visual-topbar">
                    <div class="window-dots">
                      <span></span>
                      <span></span>
                      <span></span>
                    </div>

                    <small>
                      {{ project.title }}
                    </small>
                  </div>

                  <div
                    v-if="
                      project.visual ===
                      'compiler'
                    "
                    class="compiler-dashboard"
                  >
                    <div class="compiler-sidebar">
                      <span></span>
                      <span></span>
                      <span></span>
                      <span></span>
                      <span></span>
                    </div>

                    <div class="compiler-code">
                      <div class="code-header">
                        <span>
                          main.js
                        </span>

                        <span>
                          AI Assistant
                        </span>
                      </div>

                      <div class="code-lines">
                        <i></i>
                        <i></i>
                        <i></i>
                        <i></i>
                        <i></i>
                        <i></i>
                        <i></i>
                        <i></i>
                        <i></i>
                        <i></i>
                      </div>

                      <div class="terminal">
                        <span>
                          $ npm run compile
                        </span>

                        <b>
                          ✓ Build successful
                        </b>
                      </div>
                    </div>
                  </div>

                  <div
                    v-else-if="
                      project.visual ===
                      'health'
                    "
                    class="health-dashboard"
                  >
                    <div class="health-sidebar">
                      <strong>♥</strong>

                      <i></i>
                      <i></i>
                      <i></i>
                      <i></i>
                    </div>

                    <div class="health-main">
                      <div class="health-title">
                        Dashboard
                      </div>

                      <div class="health-metrics">
                        <div>
                          <b>1,248</b>
                          <small>
                            Patients
                          </small>
                        </div>

                        <div>
                          <b>328</b>
                          <small>
                            Appointments
                          </small>
                        </div>

                        <div>
                          <b>328</b>
                          <small>
                            Doctors
                          </small>
                        </div>
                      </div>

                      <div class="health-chart">
                        <div
                          v-for="n in 7"
                          :key="n"
                          class="health-bar"
                          :style="{
                            height:
                              25 +
                              n * 8 +
                              '%',
                          }"
                        ></div>
                      </div>
                    </div>
                  </div>

                  <div
                    v-else-if="project.visual === 'finance'"
                    class="security-dashboard finance-dashboard"
                  >
                    <div class="security-sidebar finance-sidebar">
                      <strong>₹</strong>
                      <i></i>
                      <i></i>
                      <i></i>
                      <i></i>
                    </div>

                    <div class="security-main finance-main">
                      <div class="security-title finance-title">
                        NAV Analytics
                      </div>

                      <div class="threat-number finance-schemes">
                        {{ financeCounts.schemes }} Schemes
                      </div>

                      <div class="finance-stats">
                        <div>
                          <b>{{ financeCounts.navRows }}K+</b>
                          <small>NAV rows</small>
                        </div>
                        <div>
                          <b>{{ financeCounts.historyStart }}–{{ financeCounts.historyEnd }}</b>
                          <small>History</small>
                        </div>
                        <div>
                          <b>{{ financeCounts.riskMetrics }}</b>
                          <small>Risk metrics</small>
                        </div>
                      </div>

                      <svg
                        class="finance-chart"
                        viewBox="0 0 220 54"
                        preserveAspectRatio="none"
                        aria-hidden="true"
                      >
                        <path
                          d="M0 43 L20 37 L38 39 L56 28 L74 32 L94 20 L112 24 L132 13 L151 19 L171 9 L192 15 L220 4"
                        />
                      </svg>
                    </div>
                  </div>

                  <div
                    v-else
                    class="security-dashboard"
                  >
                    <div class="security-sidebar">
                      <strong>◈</strong>

                      <i></i>
                      <i></i>
                      <i></i>
                      <i></i>
                    </div>

                    <div class="security-main">
                      <div class="security-title">
                        Threat Overview
                      </div>

                      <div class="threat-number">
                        2,842
                      </div>

                      <div class="threat-grid">

                        <div class="world-map">
                          <span class="threat-dot"></span>
                          <span class="threat-dot"></span>
                          <span class="threat-dot"></span>
                          <span class="threat-dot"></span>
                        </div>

                        <div class="security-donut">
                          <div></div>
                        </div>

                      </div>
                    </div>
                  </div>

                  <div class="visual-glow"></div>
                </div>

                <div
                  class="project-icon"
                  :class="project.accent"
                >
                  <span
                    v-if="
                      project.visual ===
                      'compiler'
                    "
                  >
                    &lt;/&gt;
                  </span>

                  <span
                    v-else-if="
                      project.visual ===
                      'health'
                    "
                  >
                    ♡
                  </span>

                  <span
                    v-else-if="project.visual === 'finance'"
                  >
                    ₹
                  </span>

                  <span v-else>
                    ◈
                  </span>
                </div>

                <div class="project-content">

                  <div class="project-title-row">
                    <div>

                      <small class="project-type">
                        {{ project.type }}
                      </small>

                      <h3>
                        {{ project.title }}
                        <span>.</span>
                      </h3>

                      <p>
                        {{ project.subtitle }}
                      </p>

                    </div>

                    <span class="project-number">
                      0{{ project.id }}
                    </span>
                  </div>

                  <p class="project-description">
                    {{ project.description }}
                  </p>

                  <div class="technology-list">

                    <div
                      v-for="tech in project.technologies"
                      :key="tech.name"
                      class="technology"
                    >

                      <span
                        class="technology-icon"
                        :class="`tech-${tech.type}`"
                        :title="tech.name"
                        v-html="techIconSvg(tech.type)"
                      ></span>

                    </div>

                  </div>

                  <div class="project-footer">

                    <button
                      type="button"
                      class="details-button"
                      @click.stop="openDetails(project)"
                    >
                      <span>
                        View Details
                      </span>

                      <b>↗</b>
                    </button>

                    <a
  :href="project.url"
  target="_blank"
  rel="noopener noreferrer"
  class="demo-button"
>
  <span>
    Live 
  </span>

  <b>↗</b>
</a>

                  </div>

                </div>
              </div>

            </article>

          </div>

          <button
            class="carousel-arrow carousel-arrow-right"
            type="button"
            aria-label="Next project"
            @click.stop="goNext"
            @mousedown.stop
          >
            <span>›</span>
          </button>

        </div>

        <div class="carousel-dots">

          <button
            v-for="(project, index) in projects"
            :key="project.id"
            type="button"
            :aria-label="`Show ${project.title}`"
            :aria-current="activeIndex === index ? 'true' : undefined"
            :class="{
              active:
                activeIndex === index,
            }"
            @click="
              goToProject(index)
            "
          ></button>

        </div>

      </main>
    </div>
  </section>

  <ProjectDetailsModal
    :project="detailsProject"
    :open="!!detailsProject"
    @close="closeDetails"
  />
</template>

<style scoped>
.projects {
  --orange:#ff8a00;
  --orange-dark:#df6f00;
  --cream:#f7f0e4;
  --cream-light:#fffaf2;
  --ink:#14171d;

  position:relative;
  width:100%;
  min-height:clamp(560px, 72vh, 820px);

  margin-top:-90px;
  padding:0 52px 45px;

  overflow:hidden;

  background:
    radial-gradient(
      circle at 55% 48%,
      rgba(255,174,82,0.13),
      transparent 34%
    ),
    linear-gradient(
      135deg,
      #faf4e9 0%,
      #f5ecde 55%,
      #fbf6ed 100%
    );

  color:var(--ink);
  perspective:1600px;
}

.projects-background {
  position:absolute;
  inset:0;
  pointer-events:none;
  overflow:hidden;
}

.background-glow {
  position:absolute;
  width:700px;
  height:700px;
  left:56%;
  top:48%;

  transform:translate(-50%,-50%);
  border-radius:50%;

  background:
    radial-gradient(
      circle,
      rgba(255,142,18,0.13),
      transparent 68%
    );

  filter:blur(25px);

  opacity:0.55;
}


.background-grid {
  position:absolute;
  inset:0;
  opacity:0.17;

  background-image:
    linear-gradient(
      rgba(80,65,45,0.07) 1px,
      transparent 1px
    ),
    linear-gradient(
      90deg,
      rgba(80,65,45,0.07) 1px,
      transparent 1px
    );

  background-size:54px 54px;

  mask-image:
    radial-gradient(
      ellipse at center,
      black,
      transparent 78%
    );
}

.background-noise {
  position:absolute;
  inset:0;
  opacity:0.025;

  background-image:
    url("data:image/svg+xml,%3Csvg viewBox='0 0 180 180' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='.8' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='.4'/%3E%3C/svg%3E");
}

.projects-wrapper {
  position:relative;
  z-index:5;

  display:grid;

  grid-template-columns:
    270px
    minmax(0,1fr);

  gap:35px;

  width:100%;
  max-width:1420px;

  margin:0 auto;
}

.projects-intro {
  padding-top:9px;
  transform: none !important;
  transform-style: flat;
  perspective: none;
}

.projects-heading {
  position:relative;
}

.projects-eyebrow {
  margin-bottom:12px;

  color:var(--orange);

  font-size:13px;
  font-weight:800;
  letter-spacing:0.24em;
}

.projects-heading h2 {
  margin:0;

  color:#111722;

  font-size:
    clamp(58px,5vw,76px);

  line-height:0.9;
  font-weight:800;
  letter-spacing:-0.065em;
}

.projects-heading h2 span {
  color:var(--orange);
}

.heading-line {
  display:flex;
  align-items:center;

  width:130px;
  height:10px;

  margin-top:28px;
}

.heading-line::before {
  content:"";

  width:115px;
  height:2px;

  background:var(--orange);

  box-shadow:
    0 0 10px
    rgba(255,138,0,0.4);
}

.heading-line span {
  width:7px;
  height:7px;

  margin-left:-2px;

  border-radius:50%;

  background:var(--orange);

  box-shadow:
    0 0 12px
    rgba(255,138,0,0.5);
}

.intro-description {
  max-width:245px;

  margin:
    31px 0 27px;

  color:#68625a;

  font-size:14px;
  line-height:1.75;
}

.feature-list {
  display:flex;
  flex-direction:column;
  gap:16px;
}

.feature-item {
  display:flex;
  align-items:flex-start;
  gap:11px;
}

.feature-icon {
  flex:
    0 0 41px;

  width:41px;
  height:41px;

  display:flex;

  align-items:center;
  justify-content:center;

  border:
    1px solid
    rgba(255,138,0,0.28);

  border-radius:11px;

  background:
    rgba(255,255,255,0.48);

  color:var(--orange);

  box-shadow:
    0 7px 18px
    rgba(90,65,35,0.06);

  transition:
    transform 0.25s ease;
}

.feature-item:hover .feature-icon {
  transform:
    translateY(-2px)
    rotate(-3deg);
}

.feature-icon svg {
  width:20px;
  height:20px;
}

.feature-item strong {
  display:block;

  margin-top:1px;

  color:#1c1d21;

  font-size:12px;
  font-weight:750;
}

.feature-item span {
  display:block;

  max-width:180px;

  margin-top:3px;

  color:#81796d;

  font-size:10px;
  line-height:1.45;
}

.view-all-button {
  width: 220px;
  min-height: 50px;
  margin-top: 25px;
  padding: 0;

  display: flex;
  align-items: center;
  justify-content: center;
  gap: 14px;

  box-sizing: border-box;

  border: 1px solid var(--orange);
  border-radius: 999px;

  background: rgba(255, 255, 255, 0.4);
  color: var(--orange);

  font-family: inherit;
  font-size: 14px;
  font-weight: 750;
  line-height: 1;

  cursor: pointer;

  transition:
    transform 0.3s ease,
    background 0.3s ease,
    color 0.3s ease,
    box-shadow 0.3s ease;
}

.view-all-button > span:first-child {
  display: block;
  margin: 0;
  padding: 0;
  white-space: nowrap;
  line-height: 1;
}

.view-all-button:hover {
  color: white;
  background: var(--orange);
  transform: translateY(-2px);
  box-shadow: 0 10px 22px rgba(255, 138, 0, 0.18);
}

.button-arrow {
  display: flex;
  align-items: center;
  justify-content: center;

  width: 18px;
  height: 18px;
  flex: 0 0 18px;

  margin: 0;
  padding: 0;

  font-size: 17px;
  line-height: 18px;

  transform: translateY(-1px);
}

.view-all-button:hover .button-arrow {
  transform: translate(2px, -2px);
}

.projects-showcase {
  position:relative;
  min-width:0;
}

.carousel-area {
  position:relative;

  height:555px;

  display:flex;

  align-items:center;
  justify-content:center;

  perspective:1600px;

  overflow:visible;
}

.project-carousel {
  position:relative;

  width:
    min(850px,100%);

  height:500px;

  display:flex;

  align-items:center;
  justify-content:center;

  transform-style:
    preserve-3d;

  z-index:10;

  overflow:visible;
  pointer-events:none;
}

.project-slide {
  position:absolute;

  width:420px;
  height:485px;

  transform-style:
    preserve-3d;

  transform-origin:
    center center;

  will-change:
    transform,
    opacity;
  pointer-events:auto;
}

.project-slide.is-active {
  filter:
    drop-shadow(
      0 25px 45px
      rgba(70,53,34,0.17)
    );
}

.project-card {
  position:relative;

  width:100%;
  height:100%;

  border:
    1px solid
    rgba(255,138,0,0.3);

  border-radius:21px;

  overflow:hidden;

  background:
    var(--cream-light);

  box-shadow:
    0 18px 42px
    rgba(50,40,25,0.1);

  transform-style:
    preserve-3d;
}

.is-active .project-card {
  border-color:
    rgba(255,138,0,0.7);

  box-shadow:
    0 28px 60px
    rgba(55,43,28,0.17),
    0 0 30px
    rgba(255,138,0,0.08);
}

/* =========================================
   3D HOLOGRAM CARD
========================================= */

.project-card {
  transform-style:
    preserve-3d;

  will-change:
    transform;

  transition:
    border-color 0.4s ease,
    box-shadow 0.4s ease;
}

.project-icon {
  transform:
    translateX(-50%)
    translateZ(42px);

  transform-style:
    preserve-3d;
}

.project-content {
  transform:
    translateZ(7px);

  transform-style:
    preserve-3d;
}

.project-title-row,
.project-description,
.technology-list,
.project-footer {
  transform:
    translateZ(4px);
}

.technology-list {
  transform-style:
    preserve-3d;
}

.technology {
  transform-style:
    preserve-3d;

  transition:
    transform 0.35s ease,
    box-shadow 0.35s ease;
}

.technology:nth-child(1) {
  transform:
    translateZ(18px);
}

.technology:nth-child(2) {
  transform:
    translateZ(28px);
}

.technology:nth-child(3) {
  transform:
    translateZ(38px);
}

.technology:nth-child(4) {
  transform:
    translateZ(48px);
}

.technology:hover {
  transform:
    translateZ(62px)
    translateY(-3px)
    scale(1.06);

  box-shadow:
    0 10px 20px
    rgba(80,55,25,0.12);
}

/* =========================================
   3D PARTICLE FIELD
========================================= */

.project-particles {
  position:absolute;

  inset:10px;

  z-index:3;

  pointer-events:none;

  transform-style:
    preserve-3d;

  perspective:1000px;
}

.project-particle {
  position:absolute;

  left:var(--x);
  top:var(--y);

  width:var(--size);
  height:var(--size);

  border-radius:50%;

  background:
    var(--orange);

  box-shadow:
    0 0 7px
    rgba(255,138,0,0.8),
    0 0 18px
    rgba(255,138,0,0.35);

  opacity:0.32;

  transform:
    translateZ(var(--z));

  animation:
    particleFloat
    calc(4s + (var(--i, 1) % 5) * 0.8s)
    ease-in-out
    infinite;

  animation-delay:
    var(--delay);
}

@keyframes particleFloat {
  0%,100% {
    transform:
      translate3d(
        0,
        0,
        var(--z)
      )
      scale(0.7);

    opacity:0.16;
  }

  50% {
    transform:
      translate3d(
        8px,
        -18px,
        calc(var(--z) + 25px)
      )
      scale(1.2);

    opacity:0.78;
  }
}

/* =========================================
   3D PLATFORM DEPTH
========================================= */

.platform-system {
  transform-style:
    preserve-3d;

  perspective:900px;

  will-change:
    transform;
}

.platform-ring {
  transform-style:
    preserve-3d;

  box-shadow:
    0 0 15px
    rgba(255,138,0,0.08);
}

.platform-base {
  transform-style:
    preserve-3d;
}

.platform-top {
  transform:
    translateZ(12px);
}

.platform-bottom {
  transform:
    translateZ(0);
}

.project-visual {
  position:relative;

  width:100%;
  height:245px;

  overflow:hidden;

  border-radius:
    20px 20px 0 0;

  background:#091014;

  isolation:isolate;
}

.project-visual::before {
  content:"";

  position:absolute;

  inset:
    -100% -40%;

  z-index:25;

  pointer-events:none;

  background:
    linear-gradient(
      110deg,
      transparent 38%,
      rgba(255,255,255,0.1) 49%,
      transparent 61%
    );

  transform:
    translateX(-65%);

  animation:
    visualSweep
    5s
    ease-in-out
    infinite;
}

@keyframes visualSweep {
  0% {
    transform:
      translateX(-65%);
  }

  55%,100% {
    transform:
      translateX(65%);
  }
}

.visual-topbar {
  position:absolute;

  top:0;
  left:0;
  right:0;

  height:31px;

  display:flex;

  align-items:center;

  gap:10px;

  padding:
    0 13px;

  border-bottom:
    1px solid
    rgba(255,255,255,0.05);

  color:
    rgba(255,255,255,0.4);

  font-size:7px;

  z-index:10;
}

.window-dots {
  display:flex;
  gap:4px;
}

.window-dots span {
  width:5px;
  height:5px;

  border-radius:50%;

  background:
    rgba(255,255,255,0.35);
}

.compiler-dashboard {
  position:absolute;

  inset:
    31px 16px 15px;

  display:flex;

  overflow:hidden;

  border:
    1px solid
    rgba(255,255,255,0.06);

  border-radius:9px;

  background:#081015;
}

.compiler-sidebar {
  width:50px;

  padding-top:17px;

  border-right:
    1px solid
    rgba(255,255,255,0.06);
}

.compiler-sidebar span {
  display:block;

  width:20px;
  height:4px;

  margin:
    0 auto 12px;

  border-radius:5px;

  background:
    rgba(255,255,255,0.16);
}

.compiler-code {
  flex:1;

  padding:16px;
}

.code-header {
  display:flex;

  justify-content:
    space-between;

  margin-bottom:12px;

  color:
    rgba(255,255,255,0.5);

  font-size:7px;
}

.code-lines {
  display:flex;

  flex-direction:column;

  gap:7px;
}

.code-lines i {
  display:block;

  width:75%;
  height:3px;

  border-radius:5px;

  background:
    linear-gradient(
      90deg,
      #38bda1,
      #5975c7
    );

  transform-origin:
    left center;
}

.code-lines i:nth-child(2) {
  width:55%;
}

.code-lines i:nth-child(3) {
  width:83%;
}

.code-lines i:nth-child(4) {
  width:43%;
}

.code-lines i:nth-child(5) {
  width:69%;
}

.code-lines i:nth-child(6) {
  width:91%;
}

.code-lines i:nth-child(7) {
  width:59%;
}

.code-lines i:nth-child(8) {
  width:76%;
}

.code-lines i:nth-child(9) {
  width:49%;
}

.code-lines i:nth-child(10) {
  width:67%;
}

.terminal {
  margin-top:14px;

  padding:8px;

  border:
    1px solid
    rgba(255,255,255,0.06);

  border-radius:6px;

  background:
    rgba(0,0,0,0.35);

  font-size:6px;
}

.terminal span {
  display:block;

  color:
    rgba(255,255,255,0.4);
}

.terminal b {
  display:block;

  margin-top:5px;

  color:#4fe39b;
}

.health-dashboard {
  position:absolute;

  inset:
    31px 16px 15px;

  display:flex;

  overflow:hidden;

  border:
    1px solid
    rgba(255,255,255,0.06);

  border-radius:9px;

  background:#101b20;
}

.health-sidebar {
  width:50px;

  padding-top:15px;

  border-right:
    1px solid
    rgba(255,255,255,0.06);

  text-align:center;
}

.health-sidebar strong {
  color:#4bd8ef;
  font-size:15px;
}

.health-sidebar i {
  display:block;

  width:20px;
  height:4px;

  margin:
    13px auto;

  border-radius:5px;

  background:
    rgba(255,255,255,0.15);
}

.health-main {
  flex:1;
  padding:15px;
}

.health-title {
  color:
    rgba(255,255,255,0.7);

  font-size:9px;

  margin-bottom:10px;
}

.health-metrics {
  display:grid;

  grid-template-columns:
    repeat(3,1fr);

  gap:6px;
}

.health-metrics div {
  padding:8px;

  border:
    1px solid
    rgba(255,255,255,0.06);

  border-radius:6px;

  background:
    rgba(255,255,255,0.025);
}

.health-metrics b {
  display:block;

  color:white;

  font-size:10px;
}

.health-metrics small {
  display:block;

  margin-top:3px;

  color:
    rgba(255,255,255,0.38);

  font-size:5px;
}

.health-chart {
  height:85px;

  margin-top:10px;

  padding:
    8px 13px;

  display:flex;

  align-items:flex-end;

  gap:9px;

  border:
    1px solid
    rgba(255,255,255,0.05);

  border-radius:6px;
}

.health-bar {
  flex:1;

  min-height:15px;

  border-radius:
    4px 4px 0 0;

  background:
    linear-gradient(
      180deg,
      #54dff2,
      #3f7784
    );

  transform-origin:
    bottom center;
}

.security-dashboard {
  position:absolute;

  inset:
    31px 16px 15px;

  display:flex;

  overflow:hidden;

  border:
    1px solid
    rgba(255,255,255,0.06);

  border-radius:9px;

  background:#171315;
}

.finance-dashboard {
  background:
    radial-gradient(
      circle at 84% 12%,
      rgba(255,138,0,0.12),
      transparent 42%
    ),
    #171513;
}

.finance-sidebar strong,
.finance-title {
  color:#ff9b35;
}

.finance-main {
  padding:12px 14px;
}

.finance-schemes {
  margin-top:3px;
  font-size:17px;
}

.finance-stats {
  display:grid;
  grid-template-columns:repeat(3, 1fr);
  gap:5px;
  margin-top:7px;
}

.finance-stats div {
  display:flex;
  flex-direction:column;
  gap:2px;
  padding:5px 6px;
  border:1px solid rgba(255,255,255,0.06);
  border-radius:5px;
  background:rgba(255,255,255,0.025);
}

.finance-stats b {
  color:#fff;
  font-size:8px;
}

.finance-stats small {
  color:rgba(255,255,255,0.4);
  font-size:5px;
}

.finance-chart {
  display:block;
  width:100%;
  height:39px;
  margin-top:5px;
  overflow:visible;
}

.finance-chart path {
  fill:none;
  stroke:#ff9638;
  stroke-width:2.5;
  stroke-linecap:round;
  stroke-linejoin:round;
  stroke-dasharray:320;
  stroke-dashoffset:320;
  filter:drop-shadow(0 0 4px rgba(255,150,56,0.55));
  animation:financeChartLoop 7s ease-in-out infinite;
}

@keyframes financeChartLoop {
  0%, 20%, 85%, 100% {
    stroke-dashoffset:320;
  }

  40%, 65% {
    stroke-dashoffset:0;
  }
}

.security-sidebar {
  width:50px;

  padding-top:16px;

  border-right:
    1px solid
    rgba(255,255,255,0.06);

  text-align:center;
}

.security-sidebar strong {
  color:#ff6557;
  font-size:14px;
}

.security-sidebar i {
  display:block;

  width:20px;
  height:4px;

  margin:
    13px auto;

  border-radius:5px;

  background:
    rgba(255,255,255,0.14);
}

.security-main {
  flex:1;

  padding:15px;
}

.security-title {
  color:
    rgba(255,255,255,0.58);

  font-size:8px;
}

.threat-number {
  margin-top:5px;

  color:white;

  font-size:20px;
  font-weight:750;
}

.threat-grid {
  display:grid;

  grid-template-columns:
    1.5fr 1fr;

  gap:9px;

  height:105px;

  margin-top:8px;
}

.world-map {
  position:relative;

  overflow:hidden;

  border-radius:7px;

  background:
    radial-gradient(
      circle,
      rgba(255,70,50,0.16),
      transparent 65%
    );
}

.world-map::before {
  content:"";

  position:absolute;

  inset:16px;

  border-radius:
    45% 55% 48% 52%;

  background:
    rgba(255,255,255,0.06);

  transform:
    rotate(-7deg);
}

.threat-dot {
  position:absolute;

  width:5px;
  height:5px;

  border-radius:50%;

  background:#ff5147;

  box-shadow:
    0 0 9px
    #ff5147;
}

.threat-dot:nth-child(1) {
  top:28px;
  left:35%;
}

.threat-dot:nth-child(2) {
  top:55px;
  left:58%;
}

.threat-dot:nth-child(3) {
  top:24px;
  left:72%;
}

.threat-dot:nth-child(4) {
  top:73px;
  left:48%;
}

.security-donut {
  display:flex;

  align-items:center;
  justify-content:center;
}

.security-donut div {
  width:58px;
  height:58px;

  border:
    7px solid
    rgba(255,255,255,0.06);

  border-top-color:#ff4d43;
  border-right-color:#ff9835;

  border-radius:50%;

  animation:
    donutSpin
    5s
    linear
    infinite;
}

@keyframes donutSpin {
  to {
    transform:
      rotate(395deg);
  }
}

.project-icon {
  position:absolute;

  left:50%;
  top:213px;

  transform:
    translateX(-50%);

  width:58px;
  height:58px;

  display:flex;

  align-items:center;
  justify-content:center;

  border:
    1px solid
    currentColor;

  border-radius:13px;

  background:#fff9ef;

  color:var(--orange);

  font-size:18px;
  font-weight:800;

  z-index:30;

  box-shadow:
    0 9px 25px
    rgba(80,55,25,0.13);

  animation:
    iconFloat
    2.8s
    ease-in-out
    infinite;
}

.project-icon.blue {
  color:#21aeda;
}

.project-icon.red {
  color:#f04b3e;
}

@keyframes iconFloat {
  0%,100% {
    transform:
      translateX(-50%)
      translateY(0);
  }

  50% {
    transform:
      translateX(-50%)
      translateY(-3px);
  }
}

.project-content {
  position:relative;

  height:240px;

  padding:
    38px 21px 14px;

  background:
    linear-gradient(
      180deg,
      #fffaf2,
      #f8eee0
    );
}

.project-title-row {
  display:flex;

  justify-content:
    space-between;

  gap:10px;

  margin-bottom:8px;
}

.project-type {
  display:block;

  margin-bottom:4px;

  color:var(--orange);

  font-size:8px;
  font-weight:800;

  letter-spacing:0.15em;
}

.project-title-row h3 {
  margin:0;

  color:#17191e;

  font-size:25px;

  line-height:1;

  font-weight:800;

  letter-spacing:-0.04em;
}

.project-title-row h3 span {
  color:var(--orange);
}

.project-title-row p {
  margin:
    5px 0 0;

  color:#e87817;

  font-size:10px;
  font-weight:650;
}

.project-number {
  color:
    rgba(35,35,35,0.24);

  font-size:10px;
  font-weight:800;
}

.project-description {
  height:47px;

  margin:0;

  padding-bottom:9px;

  border-bottom:
    1px solid
    rgba(80,65,45,0.13);

  color:#676157;

  font-size:9px;

  line-height:1.5;
}

.technology-list {
  display:flex;

  align-items:center;

  gap:6px;

  margin-top:9px;

  min-height:30px;
}

.technology {
  min-width:39px;
  height:28px;

  display:flex;

  align-items:center;
  justify-content:center;

  padding:
    0 6px;

  border:
    1px solid
    rgba(75,65,52,0.12);

  border-radius:7px;

  background:
    rgba(255,255,255,0.58);

  color:#716b61;

  font-size:7px;
  font-weight:700;
}

.technology-icon {
  display:flex;
  align-items:center;
  justify-content:center;

  width:20px;
  height:20px;

  flex:0 0 20px;

  color:#6b665d;
}

.technology-icon svg {
  width:17px;
  height:17px;
  display:block;
}

.tech-react { color:#159dce; }
.tech-node { color:#58b957; }
.tech-mongo { color:#43a63a; }
.tech-websocket { color:#7a5fc7; }
.tech-docker { color:#2496ed; }
.tech-github { color:#222; }
.tech-python { color:#3776ab; }
.tech-flask { color:#222; }
.tech-ml { color:#9a58c7; }
.tech-tailwind { color:#06b6d4; }
.tech-prisma { color:#2d3748; }
.tech-postgres { color:#336791; }
.tech-jwt { color:#e19b22; }
.tech-aws { color:#e17b00; }
.tech-fastapi { color:#009688; }
.tech-sql { color:#e68a00; }
.tech-pandas { color:#7563b5; }
.tech-powerbi { color:#e5a900; }
.tech-sqlite { color:#287ab3; }
.tech-firebase { color:#f0a000; }
.tech-gemini { color:#6688d8; }

.project-footer {
  position:absolute;

  left:21px;
  right:21px;

  bottom:13px;

  display:flex;

  align-items:center;

  justify-content:
    space-between;

  gap:12px;
}

.details-button,
.demo-button {
  display:inline-flex;

  align-items:center;
  justify-content:center;

  gap:5px;

  min-height:44px;
  min-width:44px;

  border:none;

  font-family:inherit;

  font-size:9px;
  font-weight:750;

  cursor:pointer;

  transition:
    all 0.25s ease;
}

.details-button {
  padding:
    6px 0;

  background:
    transparent;

  color:var(--orange);
}

.details-button:hover {
  transform:
    translateX(3px);
}

.details-button b {
  font-size:12px;
}

.demo-button {
  padding:
    7px 13px;

  border:
    1px solid
    var(--orange);

  border-radius:8px;

  background:
    rgba(255,255,255,0.45);

  color:var(--orange);
}

.demo-button:hover {
  background:
    var(--orange);

  color:white;

  transform:
    translateY(-2px);

  box-shadow:
    0 7px 17px
    rgba(255,138,0,0.18);
}

.carousel-arrow {
  position:absolute;
  top:50%;
  width:54px;
  min-width:44px;
  height:54px;
  min-height:44px;
  margin:0;
  padding:0;

  display:flex;
  align-items:center;
  justify-content:center;

  appearance:none;
  -webkit-appearance:none;

  border:1px solid var(--orange);
  border-radius:50%;
  background:rgba(255,250,242,0.96);
  color:#29231d;
  font-family:inherit;
  cursor:pointer;

  z-index:1000;
  pointer-events:auto;
  touch-action:manipulation;
  user-select:none;

  transform:translate3d(0,-50%,80px);
  transform-style:flat;

  box-shadow:0 7px 18px rgba(70,50,30,0.08);

  transition:
    background 0.25s ease,
    color 0.25s ease,
    box-shadow 0.25s ease,
    transform 0.25s ease;
}

.carousel-arrow span {
  display:block;
  margin:0;
  font-size:30px;
  font-weight:400;
  line-height:1;
  pointer-events:none;
  transform:translateY(-2px);
}

.carousel-arrow:focus-visible {
  outline:2px solid var(--orange);
  outline-offset:4px;
}

.carousel-arrow:active {
  transform:translate3d(0,-50%,80px) scale(0.94);
}

.carousel-arrow:hover {
  color:white;
  background:var(--orange);
  transform:translate3d(0,-50%,80px) scale(1.07);
  box-shadow:0 10px 22px rgba(255,138,0,0.2);
}

.carousel-arrow-left {
  left:2px;
}

.carousel-arrow-right {
  right:2px;
}

.platform-system {
  position:absolute;

  left:50%;

  bottom:1px;

  width:690px;
  height:130px;

  transform:
    translateX(-50%);

  z-index:2;

  pointer-events:none;
}

.platform-ring {
  position:absolute;

  left:50%;
  top:51%;

  transform:
    translate(
      -50%,
      -50%
    );

  border:
    1px solid
    rgba(255,138,0,0.3);

  border-radius:50%;
}

.ring-one {
  width:290px;
  height:42px;
}

.ring-two {
  width:400px;
  height:59px;
}

.ring-three {
  width:525px;
  height:79px;
}

.ring-four {
  width:650px;
  height:100px;

  border-style:dashed;

  opacity:0.48;
}

.ring-one {
  animation:
    platformRingOne
    12s
    linear
    infinite;
}

.ring-two {
  animation:
    platformRingTwo
    16s
    linear
    infinite reverse;
}

.ring-three {
  animation:
    platformRingOne
    22s
    linear
    infinite;
}

@keyframes platformRingOne {
  from {
    transform:
      translate(-50%,-50%)
      rotateZ(0deg);
  }

  to {
    transform:
      translate(-50%,-50%)
      rotateZ(360deg);
  }
}

@keyframes platformRingTwo {
  from {
    transform:
      translate(-50%,-50%)
      rotateZ(0deg);
  }

  to {
    transform:
      translate(-50%,-50%)
      rotateZ(360deg);
  }
}

.platform-base {
  position:absolute;

  left:50%;
  bottom:0;

  width:315px;
  height:55px;

  transform:
    translateX(-50%);

  z-index:5;
}

.platform-top {
  position:absolute;

  left:0;
  top:0;

  width:100%;
  height:36px;

  border:
    1px solid
    rgba(180,150,110,0.25);

  border-radius:50%;

  background:
    linear-gradient(
      180deg,
      #fffdf9,
      #efe3d2
    );

  box-shadow:
    0 10px 25px
    rgba(65,48,30,0.13),
    inset
    0 4px 8px
    rgba(255,255,255,0.9);
}

.platform-bottom {
  position:absolute;

  left:7%;
  right:7%;

  bottom:0;

  height:23px;

  border-radius:
    0 0 50% 50%;

  background:
    linear-gradient(
      180deg,
      #e5d7c5,
      #cdbba4
    );

  box-shadow:
    0 10px 18px
    rgba(60,45,30,0.13);
}

.carousel-dots {
  position:relative;

  z-index:35;

  display:flex;

  justify-content:center;
  align-items:center;

  gap:8px;

  margin-top:-1px;
}

.carousel-dots button {
  display:grid;
  place-items:center;
  width:44px;
  min-width:44px;
  height:44px;

  padding:0;

  border:none;

  border-radius:50%;

  background:transparent;

  cursor:pointer;

  transition:
    all 0.25s ease;
}

.carousel-dots button::before {
  content:"";
  width:8px;
  height:8px;
  border-radius:50%;
  background:#d5cbbb;
  transition:all 0.25s ease;
}

.carousel-dots button.active {
  width:44px;
  border-radius:50%;
  background:transparent;
  box-shadow:none;
}

.carousel-dots button.active::before {
  width:24px;
  border-radius:999px;
  background:var(--orange);
  box-shadow:0 0 10px rgba(255,138,0,0.35);
}

.floating-cube {
  position:absolute;

  top:21px;
  right:24px;

  width:135px;
  height:135px;

  z-index:50;

  perspective:700px;

  pointer-events:none;
}

.cube {
  position:absolute;

  left:50%;
  top:48%;

  width:55px;
  height:55px;

  transform:
    translate(-50%,-50%)
    rotateX(-18deg)
    rotateY(20deg);

  transform-style:
    preserve-3d;
}

.cube-face {
  position:absolute;

  width:55px;
  height:55px;

  border:
    1px solid
    rgba(255,138,0,0.7);

  background:
    rgba(255,171,71,0.05);

  box-shadow:
    inset
    0 0 18px
    rgba(255,138,0,0.07);
}

.cube-front {
  transform:
    translateZ(27.5px);
}

.cube-back {
  transform:
    rotateY(180deg)
    translateZ(27.5px);
}

.cube-left {
  transform:
    rotateY(-90deg)
    translateZ(27.5px);
}

.cube-right {
  transform:
    rotateY(90deg)
    translateZ(27.5px);
}

.cube-top {
  transform:
    rotateX(90deg)
    translateZ(27.5px);
}

.cube-bottom {
  transform:
    rotateX(-90deg)
    translateZ(27.5px);
}

.cube-orbit {
  position:absolute;

  left:0;
  top:45px;

  width:130px;
  height:57px;

  border:
    1px dashed
    rgba(255,138,0,0.4);

  border-radius:50%;

  transform:
    rotate(-15deg);

  animation:
    orbitSpin
    7s
    linear
    infinite;
}

.orbit-two {
  transform:
    rotate(55deg);

  opacity:0.42;

  animation-duration:
    9s;
}

@keyframes orbitSpin {
  from {
    transform:
      rotate(-15deg);
  }

  to {
    transform:
      rotate(345deg);
  }
}

.cube-dot {
  position:absolute;

  width:5px;
  height:5px;

  border-radius:50%;

  background:
    var(--orange);

  box-shadow:
    0 0 9px
    var(--orange);
}

.dot-one {
  top:31px;
  left:6px;
}

.dot-two {
  right:5px;
  top:91px;
}

.dot-three {
  right:32px;
  top:5px;
}

@media (max-width:1050px) {
  .projects {
    margin-top:-100px;
    padding:
      40px 30px 42px;
  }

  .projects-wrapper {
    grid-template-columns:
      225px
      minmax(0,1fr);

    gap:24px;
  }

  .project-slide {
    width:390px;
  }

  .project-carousel {
    width:760px;
  }

  .platform-system {
    width:620px;
  }

  .ring-four {
    width:580px;
  }

  .ring-three {
    width:470px;
  }

  .ring-two {
    width:360px;
  }
}

@media (max-width:820px) {
  .projects {
    margin-top:-65px;
  }

  .projects-wrapper {
    grid-template-columns:1fr;
  }

  .projects-intro {
    max-width:600px;
    margin:0 auto;
  }

  .intro-description {
    max-width:500px;
  }

  .feature-list {
    display:grid;
    grid-template-columns:
      1fr 1fr;
  }

  .projects-showcase {
    margin-top:25px;
  }

  .project-slide {
    width:380px;
  }

  .project-carousel {
    width:720px;
  }
}

@media (max-width:620px) {
  .projects {
    margin-top:-35px;
    padding:
      45px 16px 32px;
  }

  .projects-heading h2 {
    font-size:58px;
  }

  .feature-list {
    grid-template-columns:
      1fr;
  }

  .carousel-area {
    height:515px;
  }

  .project-carousel {
    width:100%;
    height:480px;
  }

  .project-slide {
    width:315px;
    height:465px;
  }

  .project-slide.is-left {
    transform:
      translateX(-65%)
      translateY(30px)
      scale(0.62) !important;

    opacity:0.18 !important;
  }

  .project-slide.is-right {
    transform:
      translateX(65%)
      translateY(30px)
      scale(0.62) !important;

    opacity:0.18 !important;
  }

  .project-visual {
    height:235px;
  }

  .project-icon {
    top:203px;
  }

  .project-content {
    height:230px;
  }

  .carousel-arrow {
    width:44px;
    height:44px;
  }

  .carousel-arrow-left {
    left:-2px;
  }

  .carousel-arrow-right {
    right:-2px;
  }

  .platform-system {
    width:430px;
    bottom:1px;
  }

  .ring-four {
    width:410px;
  }

  .ring-three {
    width:345px;
  }

  .ring-two {
    width:275px;
  }

  .ring-one {
    width:210px;
  }

  .platform-base {
    width:235px;
  }

  .floating-cube {
    right:-10px;

    transform:
      scale(0.7);

    opacity:0.45;
  }
}

@media (max-width:390px) {
  .project-slide {
    width:292px;
  }

  .project-title-row h3 {
    font-size:23px;
  }

  .project-description {
    font-size:8px;
  }

  .project-footer {
    left:17px;
    right:17px;
  }

  .technology {
    min-width:34px;
  }
}

/* Scroll-reveal depth: the showcase feels like it is entering the page
   from above instead of simply fading in. */
.projects-showcase {
  transform-style:preserve-3d;
  transform-origin:center top;
  will-change:transform, opacity;
}

.projects-heading,
.projects-intro,
.project-slide,
.floating-cube {
  will-change:transform, opacity;
}

@media (max-width:620px) {
  .project-particles {
    opacity:0.45;
  }

  .project-particle {
    animation-duration:6s;
  }

  .projects-showcase {
    transform-style:preserve-3d;
  }
}

@media (prefers-reduced-motion:reduce) {
  .projects-showcase,
  .project-particles,
  .platform-system,
  .project-card,
  .project-particle {
    transform:none !important;
  }

  *,
  *::before,
  *::after {
    animation-duration:
      0.01ms !important;

    animation-iteration-count:
      1 !important;

    scroll-behavior:
      auto !important;

    transition-duration:
      0.01ms !important;
  }

  .finance-chart path {
    animation:none;
    stroke-dashoffset:0;
  }
}
</style>
