# Morning Motivator (with OpenAI TTS)

Wekker die je *opbelt* met een AI-gegenereerde motivatieboodschap. Twilio belt uit, TwiML **<Play>** streamt een MP3 dat live wordt gesynthetiseerd met **OpenAI TTS (gpt-4o-mini-tts)**.

## Snel starten

```bash
pnpm i
cp .env.example .env
# Vul .env in (Postgres, Twilio, OpenAI, Redis, APP_BASE_URL)
pnpm prisma:generate
pnpm prisma:migrate
pnpm dev
# In een aparte terminal: start worker
pnpm worker
```

### Flow
1) Ga naar **/profile**, vul e-mail + telefoon (E.164) en motivatieprofiel in → opslaan.
2) Ga naar **/alarms**, maak een wekker. De server plant een BullMQ job.
3) Op het tijdstip belt Twilio je nummer en haalt Twilio TwiML op bij `/api/voice/twiml?callId=...`.
4) TwiML **<Play>** verwijst naar `/api/voice/audio?callId=...`, dat:
   - je profiel ophaalt
   - motivatie-tekst genereert (GPT-5)
   - **OpenAI TTS** aanroept (**gpt-4o-mini-tts**, voice `"alloy"`) en een **MP3** terugstuurt.

## Notities
- Zorg dat `APP_BASE_URL` publiek bereikbaar is (bv. via ngrok of deploy) voor Twilio webhooks.
- In productie: voeg auth/ratelimiting toe, cache TTS indien gewenst, en hanteer retrypolicy voor jobs.
- Je kan stemmen/voices wisselen in `src/lib/openai.ts` (`ttsToMp3`).

## Stack
Next.js (App Router) • Prisma/Postgres • BullMQ/Redis • Twilio Voice • OpenAI GPT-5 (tekst) & gpt-4o-mini-tts (TTS).
