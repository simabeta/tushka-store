<div dir="rtl">

# 🚀 راهنمای استقرار — فروشگاه توشکا

## معماری استقرار

```
[مرورگر کاربر] → [هاست استاتیک: بیلد Vite] → [Supabase: DB + Auth + Storage]
                                            → [Edge Functions: درگاه پرداخت]
```

فرانت‌اند کاملاً استاتیک است (روتر hash-based) و نیازی به سرور Node/PHP ندارد.

## ۱) بیلد فرانت‌اند

```bash
# مطابق راهنمای vite-template/README.md پروژه Vite بسازید، سپس:
cp .env.example .env.local    # مقادیر VITE_SUPABASE_* را پر کنید
npm run build                 # خروجی: dist/
```

## ۲) میزبانی استاتیک

| هاست | نحوه |
|---|---|
| Vercel / Netlify / Cloudflare Pages | اتصال مخزن؛ Build: `npm run build`، Output: `dist`؛ متغیرهای محیطی را در پنل ست کنید |
| هاست اشتراکی (cPanel و…) | محتویات `dist/` را در `public_html` بگذارید؛ چون روتر hash-based است، SPA fallback لازم نیست |
| هر هاست با rewrite (اختیاری) | اگر مسیرهای بدون hash خواستید، rewrite کامل به `index.html` |

## ۳) دامنه و HTTPS
- دامنه را به هاست وصل کنید؛ SSL را فعال کنید (Let's Encrypt / Cloudflare).
- در Supabase → **Authentication → URL Configuration** مقدار Site URL و
  Redirect URLs را با دامنه نهایی به‌روزرسانی کنید (`https://دامنه/#/auth` و
  `https://دامنه/#/reset-password`).

## ۴) استقرار Edge Functions

```bash
supabase functions deploy process-payment-callback --project-ref <ref>
supabase functions deploy create-order           --project-ref <ref>
supabase functions deploy admin-set-role         --project-ref <ref>

# راز‌های سمت سرور (هرگز در فرانت‌اند نیستند)
supabase secrets set PAYMENT_MERCHANT_ID=...
supabase secrets set PAYMENT_SANDBOX=false
```

## ۵) چک‌لیست تست نهایی قبل از اعلام آمادگی

1. `npm run build` بدون خطا؛ خروجی `dist` روی هاست بالا بیاید.
2. ورود/ثبت‌نام/بازیابی با دامنه نهایی تست شود.
3. ثبت یک سفارش واقعی (پرداخت در محل) و مشاهده در پنل.
4. آپلود یک تصویر محصول توسط مدیر و نمایش در فروشگاه.
5. اجرای `docs/SECURITY-CHECKLIST.md`.
6. تست ریسپانسیو روی موبایل واقعی (safe-area ناوبری پایین).

## ۶) نگهداری
- بکاپ‌گیری: Supabase → Database → Backups (در پلن‌های حرفه‌ای) یا `pg_dump` دوره‌ای.
- پایش: Supabase → Logs (API / Auth / Edge Function).
- به‌روزرسانی وابستگی‌ها: `npm outdated` → تست کامل → deploy.
</div>
