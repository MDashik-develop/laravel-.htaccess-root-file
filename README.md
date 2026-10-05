# laravel-.htaccess-root-file
project>.htaccess
<br>
#     # ডোমেইন রুট থেকে রিকোয়েস্ট আসলে তা /public ফোল্ডারে পাঠিয়ে দিবে
#     RewriteCond %{REQUEST_URI} !^/public/
#     RewriteRule ^(.*)$ public/$1 [L]
# </IfModule>


<IfModule mod_rewrite.c>
    <IfModule mod_negotiation.c>
        Options -MultiViews -Indexes
    </IfModule>

    RewriteEngine On

    # --- সংবেদনশীল ফাইল বাইরে থেকে ব্লক করা ---
    RewriteRule ^(\.env|composer\.json|composer\.lock|package\.json|README\.md) - [F,L]
    RewriteRule (^|/)\.(?!well-known) - [F]

    # Handle Authorization Header
    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

    # Handle X-XSRF-Token Header
    RewriteCond %{HTTP:x-xsrf-token} .
    RewriteRule .* - [E=HTTP_X_XSRF_TOKEN:%{HTTP:X-XSRF-Token}]

    # Redirect Trailing Slashes If Not A Folder...
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_URI} (.+)/$
    RewriteRule ^ %1 [L,R=301]

    # --- ফোল্ডার চেক করে public ফোল্ডারে রুট করা ---
    RewriteCond %{REQUEST_URI} !^/public/
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^(.*)$ public/$1 [L]

    # যদি কেউ সরাসরি রুট ভিজিট করে
    RewriteCond %{REQUEST_URI} ^/$
    RewriteRule ^$ public/index.php [L]
</IfModule>

# অতিরিক্ত সিকিউরিটি হেডার
<IfModule mod_headers.c>
    Header set X-Frame-Options "SAMEORIGIN"
    Header set X-XSS-Protection "1; mode=block"
    Header set X-Content-Type-Options "nosniff"
</IfModule>


<br>
এই কোডে কী কী সিকিউরিটি দেওয়া হয়েছে এবং এসইও (SEO) বা গুগল ইনডেক্সিংয়ে কোনো সমস্যা হবে কি না—তা নিচে বিস্তারিত আলোচনা করা হলো:

---

### **১. এখানে কী কী সিকিউরিটি দেওয়া হয়েছে?**

এই `.htaccess` কোডের মাধ্যমে মূলত নিচের লেভেলের সিকিউরিটিগুলো নিশ্চিত করা হয়েছে:

* **সংবেদনশীল ফাইল ব্লক (`.env`, `composer.json` ইত্যাদি):** লারাভেলে আপনার ডেটাবেজ পাসওয়ার্ড, এপিআই কি (API Key) এবং সিক্রেট তথ্যগুলো `.env` ফাইলে থাকে। এই কোডের মাধ্যমে কেউ যদি সরাসরি ব্রাউজারে `[yourdomain.com/.env](https://yourdomain.com/.env)` লিখে এন্টার দেয়, তবে সার্ভার তা **ব্লাক বা ব্লক** করে দেবে (403 Forbidden দেখাবে)। এর ফলে হ্যাকাররা বা সাধারণ মানুষ আপনার গোপন ফাইল পড়তে পারবে না।
* **ডাইরেক্টরি ব্রাউজিং বন্ধ (`Options -MultiViews -Indexes`):** কেউ যদি আপনার সাইটের কোনো ফোল্ডারে (যেমন: `/uploads` বা `/storage`) সরাসরি প্রবেশ করতে চায় যেখানে কোনো `index.php` ফাইল নেই, তবে পুরো ফোল্ডারের ভেতর কী কী ফাইল আছে তার তালিকা (Directory Listing) দেখতে পাবে না। এটি ফোল্ডার স্ক্যানিং রোধ করে।
* **হিডেন ফাইল প্রোটেকশন (`.git` ইত্যাদি):** সার্ভারে থাকা বিভিন্ন হিডেন ফাইল বা ডট দিয়ে শুরু হওয়া ফাইলগুলোর অ্যাক্সেস সুরক্ষিত রাখা হয়েছে।
* **সিকিউরিটি হেডার (Security Headers):**
* `X-Frame-Options "SAMEORIGIN"`: আপনার সাইট অন্য কোনো ক্ষতিকর সাইটের ভেতরে `<iframe>`-এর মাধ্যমে লোড করে ক্লিকজ্যাকিং (Clickjacking) অ্যাটাক করতে পারবে না।
* `X-XSS-Protection "1; mode=block"`: ব্রাউজারের নিজস্ব ক্রস-সাইট স্ক্রিপ্টিং (XSS) ফিল্টার সক্রিয় রাখে।
* `X-Content-Type-Options "nosniff"`: ব্রাউজার যেন ফাইলের ভুল মাইম টাইপ (MIME-type) ধরে জোর করে কোনো কোড এক্সিকিউিউট না করতে পারে, তা নিশ্চিত করে।



---

### **২. এসইও (SEO) বা গুগল ইনডেক্সিংয়ে কি কোনো সমস্যা হবে?**

**না, এসইও বা গুগল ইনডেক্সিংয়ে কোনো নেতিবাচক বা খারাপ প্রভাব পড়বে না**, বরং এটি এসইও-এর জন্য আরও ভালো। এর কারণগুলো নিচে দেওয়া হলো:

* **সঠিক রিডাইরেক্ট (Clean URLs):** এই কোডের কারণে ব্যবহারকারী বা গুগলের ক্রলার যখন আপনার সাইটে ঢুকবে, তখন তারা `/public/` ফোল্ডার ছাড়াই সরাসরি ক্লিন ইউআরএল (`drmasumfakir.com`) দেখতে পাবে। গুগল ক্লিন এবং প্রফেশনাল ইউআরএল স্ট্রাকচার পছন্দ করে।
* **কোনো 404 বা ভুল সিগন্যাল নেই:** রিকোয়েস্টগুলো ইন্টারনালভাবে ফরোয়ার্ড করা হয় বিধায় গুগলের বট বা ক্রলারের কাছে কোনো ভুল বা ফেক রিডাইরেক্ট সিগন্যাল যায় না। এটি স্বাভাবিক নিয়মেই পেজগুলো ক্রল ও ইনডেক্স করতে পারবে।
* **সিকিউর সাইট র‌্যাংক ভালো পায়:** গুগলের অ্যালগরিদম সিকিউরড ওয়েবসাইটকে (যেমন: সংবেদনশীল ফাইল প্রোটেক্টেড থাকা) সার্চ রেজাল্টে কিছুটা প্রাধান্য দিয়ে থাকে।

সংক্ষেপে বলতে গেলে, এই কোডটি আপনার লারাভেল প্রজেক্টকে ১০০% নিরাপদ রাখার পাশাপাশি গুগলের চোখে আপনার সাইটের এসইও ফ্রেন্ডলি স্ট্রাকচার বজায় রাখতে সাহায্য করবে।
