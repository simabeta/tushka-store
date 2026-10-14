# قالب Vite فروشگاه توشکا 🐰

این پوشه همه‌چیزِ لازم برای ساخت **بیلد استاتیک Vite** را دارد — دقیقاً همان
معماری‌ای که در سندباکس Next.js اجرا می‌بینید، با همان کد مشترک.

## نحوه ساخت پروژه Vite (۵ قدم)

```bash
# 1) یک پوشه جدید بسازید و فایل‌های این قالب را داخل آن کپی کنید
mkdir tushka-vite && cp -r vite-template/* tushka-vite/

# 2) کد مشترک اپلیکیشن را کپی کنید (کاملاً framework-agnostic است)
cp -r src/tushka        tushka-vite/src/tushka
cp -r public/images     tushka-vite/public/images
cp -r public/fonts      tushka-vite/public/fonts
cp    public/favicon.svg tushka-vite/public/favicon.svg

# 3) وابستگی‌ها را نصب کنید
cd tushka-vite && npm install

# 4) فایل محیطی بسازید (به .env.example مراجعه کنید)
cp ../.env.example .env.local
#   VITE_SUPABASE_URL=https://xxxx.supabase.co
#   VITE_SUPABASE_PUBLISHABLE_KEY=sb_publishable_...یاanon key

# 5) اجرا / بیلد
npm run dev        # حالت توسعه
npm run build      # خروجی استاتیک در dist/
```

پوشه `dist/` را روی هر میزبان استاتیک (Vercel، Netlify، Cloudflare Pages،
GitHub Pages با SPA fallback، یا حتی هاست اشتراکی با پشتیبانی فایل استاتیک)
بگذارید — چون روتر اپ hash-based است، به rewrite سمت سرور هم نیازی نیست.

## نکته‌ها

- **روتینگ:** همه مسیرها با `#/` کار می‌کنند (مثلاً `#/product/pink-bunny`)؛
  روی هر میزبان استاتیکی بدون تنظیمات اضافه کار می‌کند.
- **فونت:** Vazirmatn به‌صورت محلی در `public/fonts` سرو می‌شود (بدون وابستگی
  به CDNهای گوگل).
- **Supabase:** همان `src/tushka/lib/supabase.ts` ابتدا متغیرهای
  `window.TUSHKA_*` (ست‌شده از `import.meta.env` در main.tsx) را می‌خواند.
- **حالت نمایشی:** اگر متغیرهای محیطی خالی باشند، اپ در «حالت نمایشی» با
  داده محلی بالا می‌آید و بالای صفحه بنر شفاف نمایش داده می‌شود.
- **آپدیت کد:** اگر ظاهر را تغییر دادید، `src/tushka` را دوباره کپی کنید؛
  فایل‌های این قالب تقریباً هرگز تغییر نمی‌کنند.
