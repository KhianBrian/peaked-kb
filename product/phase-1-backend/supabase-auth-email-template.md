# Supabase Auth Email Template

## Purpose

Supabase's default confirmation email feels too bare for Peaked. Use a branded confirmation template before sending invites to beta testers.

## Dashboard Location

Supabase Dashboard:

```text
Authentication -> Emails -> Confirm signup
```

## Subject

```text
Confirm your Peaked account
```

## HTML Template

Use Supabase's `{{ .ConfirmationURL }}` variable for the confirmation link.

The current generated poster lives in the app public folder:

```text
peaked-219edec1/public/peaked-email-hero.png
```

After Lovable deploys it, the production image URL should be:

```text
https://peaked.lovable.app/peaked-email-hero.png
```

```html
<div style="margin:0;background:#0f1115;padding:32px 16px;font-family:Inter,Arial,sans-serif;color:#f7f7f8;">
  <div style="max-width:600px;margin:0 auto;border:1px solid rgba(255,255,255,0.12);border-radius:22px;background:#171a21;overflow:hidden;">
    <img src="https://peaked.lovable.app/peaked-email-hero.png" alt="Peaked job-search command center" style="display:block;width:100%;height:auto;border:0;" />
    <div style="padding:30px;">
      <div style="font-size:24px;font-weight:800;letter-spacing:-0.01em;">Peaked</div>
      <p style="margin:18px 0 0;color:#f7f7f8;font-size:20px;font-weight:700;line-height:1.35;">
        Confirm your email and start building your job-search command center.
      </p>
      <p style="margin:12px 0 0;color:#c7c9d1;font-size:15px;line-height:1.6;">
        Peaked helps you turn your resume into career evidence, compare better-fit roles, tailor truthful application materials, prepare for interviews, and track what works.
      </p>
      <div style="margin:22px 0;padding:16px;border-radius:16px;background:rgba(255,255,255,0.06);border:1px solid rgba(255,255,255,0.10);">
        <p style="margin:0 0 8px;color:#f7f7f8;font-size:14px;font-weight:700;">Inside Peaked</p>
        <p style="margin:0;color:#c7c9d1;font-size:14px;line-height:1.6;">
          Career evidence profile · Remote VA job fit scoring · Resume and cover letter tailoring · Interview prep · Application tracking
        </p>
      </div>
      <a href="{{ .ConfirmationURL }}" style="display:inline-block;border-radius:999px;background:#e44b2d;color:#ffffff;text-decoration:none;font-weight:800;padding:13px 20px;">
        Confirm email
      </a>
      <p style="margin:24px 0 0;color:#8f94a3;font-size:12px;line-height:1.6;">
        If you did not create a Peaked account, you can ignore this email.
      </p>
    </div>
  </div>
</div>
```

## Poster Image Prompt

Use this prompt with ChatGPT image generation or another image model:

```text
Create a polished email hero poster for "Peaked", a job-search command center for Filipino remote job seekers and virtual assistants. Show a modern dark-mode product scene, not a generic stock image: a sleek dashboard interface with ranked job opportunities, resume evidence cards, match score rings, and application progress signals. Include subtle Philippine/remote-work cues through timezone cards, remote role tags, and professional workspace details, but no flags or clichés. Use Peaked's visual direction: deep navy/charcoal background, crisp white text areas, electric blue accents, restrained warm coral accent, premium SaaS feel, trustworthy and focused. No readable small UI text except the large brand word "Peaked". Leave safe empty space near the top-left for email copy cropping. Aspect ratio 16:9, high resolution, clean lighting, sharp interface details, no people, no fake logos other than Peaked.
```

## Redirect Settings

For local testing, set Supabase auth redirect URLs:

```text
http://localhost:8080/auth/verified
http://localhost:8080/**
```

Production URLs:

```text
https://peaked.lovable.app/auth/verified
https://peaked.lovable.app/**
```

The app sends sign-up confirmations to `/auth/verified`, which shows a confirmation page and then routes the user into onboarding.

## Notes

- Keep the email short and trustworthy.
- Do not mention beta payment or PayMongo in the confirmation email.
- Test the email by creating a new account before inviting testers.
