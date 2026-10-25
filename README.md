# MFS VEXORA

Storefront (`index.html`) plus a protected admin panel (`admin.html`), backed by Supabase (database, login, image storage). Hosted free on GitHub Pages. Everything below works from an Android phone browser.

**Files:** `index.html`, `style.css`, `script.js`, `admin.html`, `admin.css`, `admin.js`, `config.js`, `supabase.sql`, `README.md`

> Tip: in Chrome, open the ⋮ menu and turn on **Desktop site** while following the Supabase and GitHub steps. Their pages are easier to use that way.

---

## 1. Create a Supabase project
1. Go to **supabase.com** → **Start your project** → sign in (GitHub or email).
2. Tap **New project**. Name it `mfs-vexora`, set a database password (save it somewhere), pick the region closest to you, and tap **Create new project**.
3. Wait 1–2 minutes until the dashboard loads.

## 2. Run the SQL schema (this also does steps 3 and 4)
1. In the left menu tap **SQL Editor** → **New query**.
2. Open `supabase.sql`, copy **everything**, paste it into the editor, and tap **Run**.
3. You should see "Success. No rows returned". If you see a red error, copy the message and fix it before continuing. The script is safe to run again.

This creates the `admins`, `categories` and `products` tables, the `product-images` storage bucket (5 MB limit, JPG/PNG/WebP only), and all security rules.

## 3. Check the storage bucket
**Storage** in the left menu → you should see **product-images**. Nothing else to do. The SQL created it.

## 4. Check the security policies
**Authentication → Policies** (or **Database → Policies**). `products`, `categories` and `admins` should each show "RLS enabled" with policies listed. Visitors can only read published products and categories. Only authorized admins can change anything.

## 5. Create the first administrator account
1. **Authentication → Users → Add user → Create new user.**
2. Enter your email and a strong password. Tick **Auto Confirm User**. Tap **Create user**.
3. **Authentication → Sign In / Providers** (may be labelled *Providers* or *Settings*): turn **off** "Allow new users to sign up". This stops anyone from registering. Even if it were on, only users in the `admins` table can use admin functions.

## 6. Authorize the administrator
SQL Editor → New query → paste this with **your** email, then **Run**:

```sql
insert into public.admins (user_id, email)
select id, email from auth.users where email = 'YOUR-EMAIL@example.com';
```
It should say "Success. 1 row affected". If it says 0 rows, the email doesn't match the user you created.

## 7. Add your Supabase URL and key to `config.js`
1. In Supabase: **Project Settings → API Keys** (and **Data API** for the URL). Copy the **Project URL** and the **publishable key** (starts with `sb_publishable_`; the older "anon" key also works).
2. Open `config.js` and replace the two placeholder texts, keeping the quotes:
   - `PASTE_YOUR_PROJECT_URL_HERE` → your Project URL
   - `PASTE_YOUR_PUBLISHABLE_KEY_HERE` → your publishable key
3. **Never** use the *secret* or *service_role* key anywhere in these files.

On GitHub you can edit directly: open the file → pencil icon → change → **Commit changes**.

## 8. Upload the files to GitHub
1. **github.com** → sign in → **+** → **New repository**. Name it `mfs-vexora`, set it **Public**, tap **Create repository**.
2. Tap **uploading an existing file**. Choose all 8 website files (`index.html`, `style.css`, `script.js`, `admin.html`, `admin.css`, `admin.js`, `config.js`, `supabase.sql`; README optional).
3. Tap **Commit changes**. Files must sit in the repository's main folder, not inside a sub-folder.

## 9. Turn on hosting
Repository → **Settings → Pages** → under **Build and deployment** choose **Deploy from a branch**, branch **main**, folder **/(root)** → **Save**. After a minute, the page shows your link: `https://YOURNAME.github.io/mfs-vexora/`.

## 10. Allow your site for password-reset emails
Supabase → **Authentication → URL Configuration**: set **Site URL** to your GitHub Pages link, and add `https://YOURNAME.github.io/mfs-vexora/admin.html` under **Redirect URLs**. Save.

## 11. Open the admin panel and add a product
1. Visit `https://YOURNAME.github.io/mfs-vexora/admin.html` and log in.
2. **Categories** → type a name (for example *Watches*) → **Add category**.
3. Tap **+ Add Product**, pick a photo from your phone, fill in name, price, optional sale price, category, description, and the Buy Now link. Tick **Publish on the storefront**.
4. Tap **Preview** if you like, then **Save product**.
5. Open the main link. The product should appear, and **Buy Now** should open your exact link in a new tab.

## 12. Troubleshooting
| Problem | Fix |
|---|---|
| Red bar "config.js still contains placeholder values" | Step 7 not done, or the GitHub file wasn't updated. Reload after committing. |
| "Incorrect email or password" | Re-check the user in Authentication → Users. Use **Forgot password** if needed. |
| "This account is not authorized" | Step 6 didn't insert a row. Run it again with the exact email. |
| **Add Product** says "Add a category first" | Create a category under **Categories**. |
| No Add Product button | You aren't logged in as an authorized admin. It shows in the top bar and as a floating button on phones. |
| "Image upload failed… not authorized" | Re-run `supabase.sql` and confirm step 6. |
| Image is rejected | Use JPG, PNG or WebP. Phone photos are shrunk automatically. HEIC isn't supported. |
| Product saved but not on the storefront | **Publish** must be ticked. Wait a few seconds and reload. |
| Reset email link goes to the wrong page | Redo step 10. |
| Changes don't show after editing files | GitHub Pages can take ~1 minute; reload with a refresh. |

## Security notes
- Admin rights are enforced by the database (`is_admin()` + Row Level Security), not by the page.
- The publishable key is meant to be public. Never publish a secret or service_role key.
- Storefront text is inserted safely (no HTML injection), and Buy Now only opens `http(s)` links with `noopener,noreferrer`.
- Wishlist and recently viewed are saved in the visitor's own browser.

## Acceptance checklist (tick each one after setup)
- [ ] A visitor can view published products.
- [ ] A signed-out visitor sees no admin data and cannot change anything (try editing from the browser console; it must fail).
- [ ] The admin can log in; a non-admin Supabase user is turned away.
- [ ] Add Product button is visible and works on Android.
- [ ] Photo upload from the phone works.
- [ ] Product saves and appears on the storefront.
- [ ] Editing updates the storefront.
- [ ] Unpublishing removes it from the storefront.
- [ ] Delete asks for confirmation and removes it.
- [ ] Buy Now opens the saved URL for that product.
- [ ] Layout works on phone and desktop.

**Status:** these have **not** been run. They need your live Supabase project. Treat the site as ready only after you complete the checklist above.
