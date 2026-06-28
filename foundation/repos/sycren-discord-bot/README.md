# sycren-discord-bot

> Private AI-powered Discord bot for Sycren server management, moderation, and community engagement.

## Description

Internal Discord bot that manages the Sycren community server. Uses AI (OpenAI API) for content moderation, ticket classification, FAQ auto-replies, and community analytics. Built with Discord.js v14 and TypeScript.

## Tech Stack

- Node.js 20 LTS
- TypeScript 5+ (strict)
- Discord.js v14
- OpenAI API (GPT-4o-mini)
- Supabase / PostgreSQL (via sycren-db, optional)
- Railway / Fly.io (hosting)

## Features (MVP)

- AI-powered content moderation (auto-mod, spam filtering)
- Slash command `/ticket` with AI classification (bug / question / sales)
- Welcome system with auto-role assignment
- Verification system (captcha)
- Command `/roadmap` — displays current milestone
- FAQ auto-reply via AI in help channels
- Moderation logging in private #mod-log channel

## Repository

`github.com/sycren/sycren-discord-bot` (private)
