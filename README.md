# Clinic Appointment Booking Agent (n8n)

An AI receptionist that books clinic appointments through a chat interface. It checks Google Calendar for availability, books the slot, and saves the patient record to Google Sheets.

## Demo

https://github.com/dineshmete/clinic-appointment-booking-agent/blob/main/Video%20Project%203.mp4

The demo shows a patient booking a checkup through the chat: a busy slot, an alternate slot offer, the booking, and the entries in Google Calendar and Google Sheets.

## Workflow

Chat Trigger → AI Agent, with:
- **Groq Chat Model** (openai/gpt-oss-20b, temperature 0)
- **Window Buffer Memory** (per chat session)
- **Check Calendar Availability** (Google Calendar, Availability)
- **Book Appointment** (Google Calendar, Create Event)
- **Save Patient Record** (Google Sheets, Append Row)

## Problems I faced and how I fixed them

1. **Local model skipped tool calls.** A local Ollama model (qwen2.5:7b, 8 GB RAM) often ignored the tools. I switched to a hosted Groq model, which calls tools reliably.
2. **Wrong dates for relative words like "kal".** The model sometimes misread "tomorrow". I added the current date, weekday, and tomorrow's date (IST) to the system prompt, so the agent converts relative dates to exact dates.
3. **Double booking.** The agent always checks the calendar before booking. If the slot is busy, it offers the two nearest free slots.
4. **Confirming without actually booking.** The prompt says to confirm only after both the booking and save tools succeed.
5. **Malformed tool calls.** The model sometimes produced tool calls in the wrong format. Setting temperature to 0 and trying a larger model helped.

## Known limitations

- Patient identity is not verified. Anyone with the link can enter any name and number. OTP verification is the next step.
- Access is protected with Basic Auth only, not per-user login.
- The model sometimes skips saving the patient record or misreads a date. Booking results are verified from the execution logs, the calendar, and the sheet, not only from the chat reply.
- Free-tier Groq has rate limits (8000 tokens per minute), so occasional waits are expected.

## Next steps

- OTP verification for phone numbers
- WhatsApp integration (Twilio/Meta setup was started but paused)
- Making the booking steps fixed workflow steps, so saving never gets skipped
- Deployment outside n8n Cloud

## Tech

n8n Cloud · Groq · Google Calendar API · Google Sheets API · Basic Auth
