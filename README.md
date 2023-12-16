# Next.js App Router Course - Starter

This is the starter template for the Next.js App Router Course. It contains the starting code for the dashboard
application.

For more information, see the [course curriculum](https://nextjs.org/learn) on the Next.js Website.

## Checkout Project

```
npx create-next-app@latest nextjs-dashboard --use-npm --example "https://github.com/vercel/next-learn/tree/main/dashboard/starter-example"
```

```
npm i
```

```
npm run dev
```

## Styles

### Import Global Styles

```tsx
import '@/app/ui/global.css';
```

### Css Modules

_home.module.css:_

```css
.shape {
    height: 0;
    width: 0;
    border-bottom: 30px solid black;
    border-left: 20px solid transparent;
    border-right: 20px solid transparent;
}
```

```tsx
import styles from '@/app/ui/home.module.css';

//...
<div className="flex flex-col justify-center gap-6 rounded-lg bg-gray-50 px-6 py-10 md:w-2/5 md:px-20">
  <div className={styles.shape}></div>
  ;
  // ...
```

### CLSX: Conditional Styling

```tsx
import clsx from 'clsx';

export default function InvoiceStatus({status}: { status: string }) {
  return (
    <span
      className={clsx(
        'inline-flex items-center rounded-full px-2 py-1 text-sm',
        {
          'bg-gray-100 text-gray-500': status === 'pending',
          'bg-green-500 text-white': status === 'paid',
        },
      )}
    >
      // ...
    </span>
  );
}
```

## Optimize Fonts & Images

### Add Font

_fonts.ts:_

```ts
import {Inter} from 'next/font/google';

export const inter = Inter({subsets: ['latin']});
```

_layout.tsx:_

```tsx
import '@/app/ui/global.css';
import {inter} from '@/app/ui/fonts';

export default function RootLayout({
                                     children,
                                   }: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
    <body className={`${inter.className} antialiased`}>{children}</body>
    </html>
  );
}
```

### Add Images

_page.tsx:_

```tsx
import Image from 'next/image';

export default function Page() {
  return (
    // ...
    <div className="flex items-center justify-center p-6 md:w-3/5 md:px-28 md:py-12">
      {/* Add Hero Images Here */}
      <Image
        src="/hero-desktop.png"
        width={1000}
        height={760}
        className="hidden md:block"
        alt="Screenshots of the dashboard project showing desktop version"
      />
    </div>
    //...
  );
}
```

## Pages & Layouts

page.tsx is a special Next.js file that exports a React component,
and it's required for the route to be accessible.
In your application, you already have a page file: /app/page.tsx -
this is the home page associated with the route /.

_/app/dashboard/page.tsx:_

```tsx
export default function Page() {
  return <p>Dashboard Page</p>;
}
```

One benefit of using layouts in Next.js is that on navigation,
only the page components update while the layout won't re-render.
This is called partial rendering.

_/app/dashboard/layout.tsx:_

```tsx
import SideNav from '@/app/ui/dashboard/sidenav';

export default function Layout({children}: { children: React.ReactNode }) {
  return (
    <div className="flex h-screen flex-col md:flex-row md:overflow-hidden">
      <div className="w-full flex-none md:w-64">
        <SideNav/>
      </div>
      <div className="flex-grow p-6 md:overflow-y-auto md:p-12">{children}</div>
    </div>
  );
}
```

## Navigation

In Next.js, you can use the <Link /> Component to link between pages in your application.

<Link> allows you to do client-side navigation with JavaScript.

```tsx
import Link from 'next/link';

// ...

<Link
  key={link.name}
  href={link.href}
  className="flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3"
>
  <LinkIcon className="w-6"/>
  <p className="hidden md:block">{link.name}</p>
</Link>;
```

### Active Link

Since `usePathname()` is a hook, you'll need to turn nav-links.tsx into a Client Component.

_nav-links.tsx:_

```tsx
'use client';

import {usePathname} from 'next/navigation';
import clsx from 'clsx';

export default function NavLinks() {
  const pathname = usePathname();
  // ...
}
```

You can use the clsx library to conditionally apply class names.

## Fetching Data

Page is an async component. This allows you to use await to fetch data.

```tsx
import {fetchRevenue} from '@/app/lib/data';

export default async function Page() {
  const revenue = await fetchRevenue();
  return (
    <main>
      <RevenueChart revenue={revenue}/>
    </main>
  );
}
```

## Static Rendering

With static rendering, data fetching and rendering happens on the server at build time (when you deploy) or during
revalidation.
The result can then be distributed and cached in a Content Delivery Network (CDN).

- Faster Websites - Prerendered content can be cached and globally distributed. This ensures that users around the world
  can access your website's content more quickly and reliably.
- Reduced Server Load - Because the content is cached, your server does not have to dynamically generate content for
  each user request.
- SEO - Prerendered content is easier for search engine crawlers to index, as the content is already available when the
  page loads. This can lead to improved search engine rankings.

Static rendering is useful for UI with no data or data that is shared across users, such as a static blog post or a
product page. It might not be a good fit for a dashboard that has personalized data that is regularly updated.

The opposite of static rendering is dynamic rendering.

## Dynamic Rendering

With dynamic rendering, content is rendered on the server for each user at request time (when the user visits the page).
There are a couple of benefits of dynamic rendering:

Real-Time Data - Dynamic rendering allows your application to display real-time or frequently updated data. This is
ideal for applications where data changes often.
User-Specific Content - It's easier to serve personalized content, such as dashboards or user profiles, and update the
data based on user interaction.
Request Time Information - Dynamic rendering allows you to access information that can only be known at request time,
such as cookies or the URL search parameters.

You can use a Next.js API called unstable_noStore inside your Server Components or data fetching functions to opt out of static rendering.

``` typescript
import { unstable_noStore as noStore } from 'next/cache';
 
export async function fetchRevenue() {
  noStore();
  // ...
}
```

## My Personal Thoughts & Annoyances /w React & NextJS as an Angular Developer

- TSX and JSX files are pretty ugly, all the logic and syntax clutters the html (similar to libraries like Tailwind)
    - In my opinion html itself and aria rules for accessibility are plenty of complexity for a template file
- All pages are called page.tsx, this is pretty annoying navigating to specific page files
- All the page route folders make for a lot of nesting when there are a lot of subroutes
- What's the point of splitting layout and page, what is a page/template if not your layout
    - What goes where, it's all pretty confusing to me
    - This doesn't make it easier to find the code that needs changes at all
    - Why not just partially rerender components, do you really need a layout for that?
    - If something doesn't need re-rendering just define it in the parent
    - A layout just looks like an unnecessary component between parent and child components to me
- Having to declare components as being used by the client when using hooks seems like something that could be derived
    - The console seems to know what is going on pretty well, so why doesn't the app?

---

&copy; H3AR7B3A7, December 2023
