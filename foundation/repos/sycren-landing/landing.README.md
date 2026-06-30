# sycren-landing

> Public-facing landing page for Sycren (sycren.com)

## Documentation
- [[landing.README]] — This file
- [[landing.requirements]] — Full PRD
- [[landing.todo]] — Implementation plan

## Dependencies
- [[design-system.README]] — UI components and design tokens
- [[infra.README]] — CI/CD and deployment config

## Downstream
- [[studio.README\|sycren-studio]] — Quote CTA redirects to studio.sycren.com
- [[app.README\|sycren-app]] — Waitlist data feeds into app user base

## Description

The official landing page of Sycren — a unified platform for software development, cloud hosting, domain management, and cybersecurity. Built with Next.js 14+ (App Router), TypeScript, and Tailwind CSS. Deployed on Vercel.

## Tech Stack

- Next.js 14+ (App Router)
- TypeScript 5+ (strict mode)
- Tailwind CSS 3.4+
- Framer Motion 11+
- React Hook Form 7+
- Zod 3+
- Lucide React

## Status

**Pre-launch (Fase 0)** — Both founders are 16 years old. The page collects waitlist contacts for when Sycren opens commercially at age 18.

## Features

- 9-section single-page layout (Hero, Divisions, How It Works, Tech Stack, Team, Pricing, Waitlist Form, Footer)
- Dark / Light / Black theme switcher with localStorage persistence
- Interactive animated grid in hero section
- Waitlist form with email notifications via Resend
- Honeypot anti-spam protection
- "Coming soon" badges for Cloud, Domains, Security divisions
- Dynamic waitlist counter ("X companies already waiting")
- Fully responsive (mobile, tablet, desktop)
- SEO optimized (Open Graph, Schema.org, sitemap, robots.txt)
- WCAG AA accessible

## URL

https://sycren.com
