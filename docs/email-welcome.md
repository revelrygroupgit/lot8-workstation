# Waitlist Welcome Email Setup

## The email (copy)

**Subject:** You're on the Lot 8 list 👀

---

Hi {{name}},

You're in.

Lot 8 is Australia's first live commerce platform — where creators stream products they actually believe in, brands sell at full retail price, and buyers shop without ever leaving the stream.

We're kicking off a private pilot with 5 creators and 5 brands. You'll hear from us personally when your spot opens up.

In the meantime:
→ Follow us on Instagram: @la.live
→ Know a creator or brand who should be in the room? Reply and introduce us.

Watch. Chat. Buy.

— The Lot 8 team
Arjun, Josh, Jamie & Antonio

---

## How to wire it up (Resend + Supabase Edge Function)

Resend is the simplest option: free tier sends 3,000 emails/month, first-class Supabase integration.

### Step 1 — Resend account

1. Sign up at https://resend.com
2. Add + verify your sending domain (or use `onboarding@resend.dev` for testing)
3. Create an API key at https://resend.com/api-keys — copy it

### Step 2 — Store the Resend API key in Supabase

In the Supabase dashboard → Settings → Edge Functions → **Add secret**:

```
RESEND_API_KEY = re_xxxxxxxxxxxx
```

### Step 3 — Create the Edge Function

In Supabase dashboard → Edge Functions → New function → name it `send-welcome-email`

Paste this code:

```typescript
import { serve } from 'https://deno.land/std@0.168.0/http/server.ts'

const RESEND_API_KEY = Deno.env.get('RESEND_API_KEY')!
const FROM_EMAIL = 'Lot 8 <team@revelrygroup.co>'  // must match verified domain

serve(async (req) => {
  const { name, email } = await req.json()

  const html = `
    <div style="font-family:'Helvetica Neue',Arial,sans-serif;max-width:560px;margin:0 auto;color:#1A1A1A;background:#fff;padding:40px 32px">
      <div style="font-size:28px;font-weight:900;letter-spacing:-.01em;margin-bottom:32px">
        <span style="color:#FF5C00">LOT</span> 8
      </div>
      <p style="font-size:16px;color:#444;line-height:1.6">Hi ${name},</p>
      <p style="font-size:24px;font-weight:800;color:#0A0A0A;margin:24px 0">You're in. 👀</p>
      <p style="font-size:15px;color:#444;line-height:1.7">
        Lot 8 is Australia's first live commerce platform — where creators stream products
        they actually believe in, brands sell at full retail price, and buyers shop without
        ever leaving the stream.
      </p>
      <p style="font-size:15px;color:#444;line-height:1.7">
        We're kicking off a private pilot with 5 creators and 5 brands.
        You'll hear from us personally when your spot opens up.
      </p>
      <div style="margin:32px 0;padding:20px 24px;background:#F5F5F5;border-radius:8px">
        <p style="font-size:13px;font-weight:700;color:#888;text-transform:uppercase;letter-spacing:.08em;margin:0 0 10px">In the meantime</p>
        <p style="font-size:14px;color:#444;margin:6px 0">→ Follow us on Instagram: <a href="https://instagram.com/la.live" style="color:#FF5C00">@la.live</a></p>
        <p style="font-size:14px;color:#444;margin:6px 0">→ Know a creator or brand who should be in the room? Reply and introduce us.</p>
      </div>
      <p style="font-size:16px;font-weight:700;color:#0A0A0A;margin:32px 0 4px">Watch. Chat. Buy.</p>
      <p style="font-size:14px;color:#888;margin:0">— Arjun, Josh, Jamie & Antonio</p>
      <div style="margin-top:40px;padding-top:24px;border-top:1px solid #E5E5E5;font-size:11px;color:#AAA">
        You're receiving this because you joined the Lot 8 waitlist at lot8-workstation.vercel.app.<br>
        Reply to unsubscribe.
      </div>
    </div>`

  const res = await fetch('https://api.resend.com/emails', {
    method: 'POST',
    headers: { 'Authorization': `Bearer ${RESEND_API_KEY}`, 'Content-Type': 'application/json' },
    body: JSON.stringify({
      from: FROM_EMAIL,
      to: email,
      subject: "You're on the Lot 8 list 👀",
      html,
    }),
  })

  const data = await res.json()
  return new Response(JSON.stringify(data), {
    headers: { 'Content-Type': 'application/json' },
    status: res.status,
  })
})
```

Deploy it — Supabase will give you a function URL like:
```
https://keywwvhzlgryfbqhbpim.supabase.co/functions/v1/send-welcome-email
```

### Step 4 — Call it from the landing page

In `index.html`, find the `lpSubmit()` function. After the successful insert, add:

```javascript
// After: else { msg.style.color=G; ...
// Add before the closing brace:
fetch('https://keywwvhzlgryfbqhbpim.supabase.co/functions/v1/send-welcome-email', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json', 'Authorization': `Bearer ${SB_ANON_KEY}` },
  body: JSON.stringify({ name, email }),
}).catch(() => {}); // fire and forget — don't block the UI
```

Where `SB_ANON_KEY` is the anon key already defined at the top of the script.

### Step 5 — Test it

Join the waitlist on the landing page with your own email and confirm it arrives.
Check the Edge Function logs in Supabase dashboard → Edge Functions → send-welcome-email → Logs.
