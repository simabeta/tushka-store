<div dir="rtl">

# 💳 راهنمای اتصال درگاه پرداخت ایرانی

## وضعیت فعلی لایه پرداخت

| مؤلفه | وضعیت |
|---|---|
| جدول `payments` (authority، ref_id، gateway_response) | ✅ آماده |
| وضعیت‌های پرداخت (`pending/paid/failed/refunded`) | ✅ آماده |
| ثبت سفارش بدون پرداخت آنلاین («پرداخت در محل») | ✅ فعال |
| تأیید سمت سرور با Edge Function | ✅ نمونه زرین‌پال نوشته شده |
| صفحه پرداخت آنلاین (انتقال به درگاه) | ⏳ پس از پیکربندی درگاه |

**قانون طلایی پیاده‌سازی‌شده:** سفارش هرگز فقط با پاسخ مرورگر «پرداخت‌شده»
نمی‌شود؛ `paid` فقط از مسیر verify سمت سرور ثبت می‌گردد.

## گام‌های اتصال زرین‌پال

1. **دریافت مرچنت:** در [zarinpal.com](https://zarinpal.com) پنل بسازید و `merchant_id` بگیرید.
2. **ذخیره راز:**
   ```bash
   supabase secrets set PAYMENT_MERCHANT_ID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   supabase secrets set PAYMENT_SANDBOX=false
   ```
3. **استقرار تابع:**
   ```bash
   supabase functions deploy process-payment-callback
   ```
4. **درگاه را به جریان خرید وصل کنید:** در `src/tushka/pages/CheckoutPage.tsx`
   پس از `placeOrder`، اگر `paymentMethod === 'online'` بود:
   - با Edge Function دیگری (یا همان تابع با پارامتر action) درخواست
     `https://payment.zarinpal.com/v4/payment/request.json` بزنید و رکورد
     `payments.authority` را ذخیره کنید؛
   - کاربر را به `https://payment.zarinpal.com/pg/StartPay/{authority}` بفرستید؛
   - در صفحه بازگشت (`/#/payment-callback`) همان authority را به
     `process-payment-callback` بدهید تا verify و ثبت `paid` انجام شود.
5. **تست sandbox:** تا قبل از تأیید نهایی، `PAYMENT_SANDBOX=true` و آدرس
   `sandbox.zarinpal.com` برای تست بدون پول واقعی.

## درگاه‌های دیگر (IDPay، SnappPay، …)
الگو همین است: ذخیره authority/توکن در `payments` → بازگشت کاربر → verify
**سمت سرور** → به‌روزرسانی سفارش. کافیست در `process-payment-callback` شاخه
`provider` را برای درگاه جدید اضافه کنید.

## نکات امنیتی
- مرچنت/کلیدهای درگاه فقط در `supabase secrets` — هرگز در کد فرانت.
- مبلغ verify باید با `payments.amount` (ثبت‌شده در سرور) برابر باشد.
- `code=101` زرین‌پال یعنی قبلاً تأیید شده — idempotent رفتار کنید (پیاده‌سازی شده).
</div>
