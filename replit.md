# توازن - Tawazon AI

تطبيق موبايل ذكي للتغذية والصيام المتقطع ومتابعة الوزن، مع تحليل الوجبات بالذكاء الاصطناعي ونظام اشتراكات متكامل.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080)
- `pnpm --filter @workspace/tawazon run dev` — run Expo app (dev)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Mobile: Expo 54, React Native 0.81, expo-router 6
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- Payments: RevenueCat (iOS + Android in-app purchases)
- AI: OpenAI GPT-4.1 Vision (food analysis), GPT-4.1 (coach)
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `artifacts/tawazon/` — Expo mobile app
- `artifacts/tawazon/app/` — Expo Router screens
- `artifacts/tawazon/lib/revenuecat.tsx` — RevenueCat subscription provider
- `artifacts/tawazon/i18n/locales/` — 10 language translations
- `artifacts/api-server/src/routes/` — API routes (auth, ai, subscriptions, admin)
- `artifacts/api-server/src/db/schema.ts` — DB schema (source of truth)
- `scripts/src/seedRevenueCat.ts` — RevenueCat seed script (idempotent)

## Architecture decisions

- RevenueCat project `projc49d134c` (pre-existing, cannot create new)
- `Constants.appOwnership === "expo"` used to detect Expo Go and skip RevenueCat
- Custom `LanguageContext` with `useTranslation()` — no i18next
- RTL enabled only for Arabic and Urdu
- Admin access via secret gesture: tap version number 5× on profile screen
- Usage limits stored in DB, editable from admin dashboard

## Product

- **Free tier**: limited AI analyses, coach queries, reports per month
- **Premium Monthly**: 29.99 SAR/month — unlimited access
- **Premium Annual**: 249.99 SAR/year — unlimited access (~58% savings)
- Features: food photo analysis, fasting tracker, weight tracker, AI nutrition coach, meal history, compatibility with 6 diet plans

## User preferences

- Arabic-first UI, RTL support
- No emojis in code unless user requests

## Launch checklist

1. Deploy API server → get production domain → set `EXPO_PUBLIC_DOMAIN` in `eas.json`
2. Create EAS account at expo.dev → run `eas login`
3. Run `eas build --platform all --profile production` from `artifacts/tawazon/`
4. Fill in `eas.json` → `appleId`, `ascAppId`, `appleTeamId` for iOS
5. Fill in `eas.json` → `serviceAccountKeyPath` for Android
6. Run `eas submit --platform all` to submit to App Store & Google Play

## RevenueCat IDs

- Project: `projc49d134c`
- iOS app key: `EXPO_PUBLIC_REVENUECAT_IOS_API_KEY` (env var)
- Android app key: `EXPO_PUBLIC_REVENUECAT_ANDROID_API_KEY` (env var)
- Entitlement: `premium`
- Packages: `$rc_monthly` (29.99 SAR), `$rc_annual` (249.99 SAR)

## Gotchas

- RevenueCat requires Dev Build (not Expo Go) for real purchases
- `pnpm run dev` at workspace root is blocked — use workflows or `--filter`
- After DB schema changes: `pnpm --filter @workspace/db run push`
- After OpenAPI spec changes: `pnpm --filter @workspace/api-spec run codegen`

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
