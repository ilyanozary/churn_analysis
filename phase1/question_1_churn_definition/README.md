README — Question 1 (Persian)

موضوع: تعریف Churn برای هر پرسونا

هدف
- تعریف یک معیار روشن و تکرارپذیر برای ریزش کاربران (Churn) به ازای هر پرسونا و تولید جداول و نمودارهای قابل گزارش برای ذی‌نفعان.

داده‌ها (ورودی‌ها)
- nmf_clusters_continent_pronouns_age_20.csv  
  - توضیح: شامل ویژگی‌های کاربران، ستون‌های مدت‌زمان (total_duration, active_duration)، شناسهٔ کاربر و ستون پرسونا (cluster_nmf_20).
- 2024_expertise_shape_final.csv
  - توضیح: شامل شکلِ خبرگی کاربران؛ این فایل موجود بود و بر اساس `userid` با دادهٔ nmf ادغام شد.

اطلاعات اجرا (run info)
- اجرا شده در: 2025-08-16
- نسخهٔ آستانهٔ پیش‌فرض در نوت‌بوک: `THRESHOLD_GAP_MONTHS = 12` (ماه)
- تذکر: ارقام عددی در بخش نتایج از اجرای نوت‌بوک در تاریخ بالا به‌دست آمده‌اند؛ برای بازتولید دقیق همان ارقام، نوت‌بوک را همانند توضیحات زیر اجرا کنید.

مهم‌ترین مراحل انجام‌شده و چرایی هر مرحله (کوتاه و دقیق)

- Normalize user id
  - کار انجام‌شده: ستون شناسهٔ کاربر در هر دیتافریم به نام `userid` نرمال‌سازی شد.
  - چرایی: یک کلید مشترک و یکنواخت برای ادغام دقیق مورد نیاز است؛ در صورت عدم‌یکنواختی، merge ناقص یا اشتباه رخ می‌دهد.

- Normalize expertise column
  - کار انجام‌شده: ستون مرتبط با خبرگی در فایل expertise به نام `expertise_shape` تغییر نام یافت و برای ادغام استفاده شد.
  - چرایی: نام یکنواخت ستون، تحلیل‌های گروهی و ایجاد جداول متقاطع را ممکن و ساده می‌کند.

- Coerce durations to numeric
  - کار انجام‌شده: تبدیل `total_duration` و `active_duration` به نوع عددی و جایگزینی مقادیر نامعتبر با 0.
  - چرایی: محاسبات تفاضل زمانی (inactive_gap) فقط با داده‌های عددی معنا دارد؛ پاک‌سازی مقادیر ناصحیح از بروز خطا جلوگیری می‌کند.

- Compute churn label
  - کار انجام‌شده: محاسبهٔ `inactive_gap = total_duration - active_duration` و برچسب‌گذاری `y_churn = 1` اگر `inactive_gap >= THRESHOLD_GAP_MONTHS`.
  - چرایی: تعریف شفاف و قابل توضیح برای ریزش که قابل تنظیم و سنجش حساسیت است.

- Persona aggregation
  - کار انجام‌شده: گروه‌بندی بر حسب `cluster_nmf_20` و محاسبهٔ تعداد کاربران، تعداد churners، نرخ ریزش و میانگین inactive_gap برای هر پرسونا.
  - چرایی: اولویت‌بندی پرسوناها بر اساس اندازه و شدت ریزش برای تصمیم‌گیری محصول/بازاریابی.

- Visualization
  - کار انجام‌شده: نمودار میله‌ای نرخ ریزش برای پرسوناها (قابل ذخیره و ارائه).
  - چرایی: نمایش بصری تفاوت‌ها برای ذی‌نفعان و تسهیل تفسیر داده.

- Persona × Expertise crosstab
  - کار انجام‌شده: ایجاد جدول متقاطع که میانگین `y_churn` را برای ترکیب پرسونا × expertise نشان می‌دهد.
  - چرایی: بررسی تعامل بین پرسونا و شکلِ خبرگی تا مشخص شود آیا خبرگی الگوهای ریزش را تغییر می‌دهد.

- Save outputs
  - کار انجام‌شده: خروجی‌ها به صورت CSV در پوشهٔ مربوط به این سوال ذخیره شدند.
  - چرایی: تولید خروجی‌های قابل‌اشتراک برای گزارش‌دهی و داشبورد.

چگونه نوت‌بوک را اجرا/بازتولید کنیم (quick reproducibility)
1) باز کردن نوت‌بوک: `/Users/khaneapple/Documents/freelance/analytic_ai/phase1_q1_churn_definition.ipynb`.
2) در صورت نیاز مقدار آستانه را در سلول مربوطه تغییر دهید (`THRESHOLD_GAP_MONTHS`).
3) هر سلول را از بالا به پایین اجرا کنید (Run All cells) تا خروجی‌های CSV تولید شوند.
4) خروجی‌ها در مسیر زیر ذخیره می‌شوند: `phase1/question_1_churn_definition/outputs`.

خلاصهٔ نتایج (اعداد از اجرای نمونه در 2025-08-16)
- اندازهٔ داده‌ها:
  - `nmf`: 92,289 ردیف
  - `expertise`: 556,445 ردیف
- توزیع ریزش در اجرا:
  - non-churn = 57,707
  - churn = 34,582
  - نرخ کلی ≈ 37.5%
- نمونه نتایج برجسته:
  - بالاترین نرخ ریزش: persona `cluster 18` — ≈ 57.9%
  - پایین‌ترین نرخ ریزش: persona `cluster 14` — ≈ 18.7%
- مدل مرجع (خلاصه): LogisticRegression — ROC‑AUC ≈ 0.739، PR‑AUC ≈ 0.644

فایل‌های خروجی (مسیر محلی)
- `/Users/khaneapple/Documents/freelance/analytic_ai/phase1/question_1_churn_definition/outputs/phase1_q1_churn_by_persona.csv`
- `/Users/khaneapple/Documents/freelance/analytic_ai/phase1/question_1_churn_definition/outputs/phase1_q1_churn_persona_by_expertise.csv`

محدودیت‌ها و نکات احتیاطی
- تعریف churn در این نوت‌بوک بر اساس اختلاف دو ستون duration است؛ اگر داده‌های timestamp دقیق (مثلاً تاریخ آخرین فعالیت) در دسترس باشند، تحلیل بقا (survival analysis / time-to-churn) دقیق‌تر و معتبرتر خواهد بود.
- مقادیر و اولویت‌ها حساس به مقدار `THRESHOLD_GAP_MONTHS` و پیش‌پردازش داده‌ها هستند؛ پیش از تصمیم‌گیری استراتژیک، تحلیل حساسیت با آستانه‌های مختلف توصیه می‌شود.

پیشنهادات عملی بعدی
- رسم heatmap برای persona × expertise و ذخیره تصویر برای گزارش.
- اجرای تحلیل حساسیت با آستانه‌های 6، 9 و 12 ماه و گزارش تغییرات در جدول.
- اضافه‌کردن یک صفحهٔ خلاصهٔ انگلیسی برای ذی‌نفعان بین‌المللی.

English one‑line summary
- We defined churn as inactive_gap >= 12 months, computed churn rates per persona, saved CSV outputs, and obtained an overall churn rate ≈ 37.5% (run on 2025-08-16).
