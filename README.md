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

export default function InvoiceStatus({ status }: { status: string }) {
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

```typescript
import { Inter } from 'next/font/google';

export const inter = Inter({ subsets: ['latin'] });
```

_layout.tsx:_

```tsx
import '@/app/ui/global.css';
import { inter } from '@/app/ui/fonts';

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

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex h-screen flex-col md:flex-row md:overflow-hidden">
      <div className="w-full flex-none md:w-64">
        <SideNav />
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
  <LinkIcon className="w-6" />
  <p className="hidden md:block">{link.name}</p>
</Link>;
```

### Active Link

Since `usePathname()` is a hook, you'll need to turn nav-links.tsx into a Client Component.

_nav-links.tsx:_

```tsx
'use client';

import { usePathname } from 'next/navigation';
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
import { fetchRevenue } from '@/app/lib/data';

export default async function Page() {
  const revenue = await fetchRevenue();
  return (
    <main>
      <RevenueChart revenue={revenue} />
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

```typescript
import { unstable_noStore as noStore } from 'next/cache';

export async function fetchRevenue() {
  noStore();
  // ...
}
```

## Streaming

There are two ways you implement streaming in Next.js:

- At the page level, with the `loading.tsx` file.
- For specific components, with `<Suspense>`.

### Loading.tsx

- `loading.tsx` is a special Next.js file built on top of Suspense, it allows you to create fallback UI to show as a replacement while page content loads.
- Since `<Sidebar>` is static, so it's shown immediately. The user can interact with `<Sidebar>` while the dynamic content is loading.
- The user doesn't have to wait for the page to finish loading before navigating away (this is called interruptable navigation).

```typescript
export default function Loading() {
  return <div>Loading...</div>;
}
```

Since `loading.tsx` is a level higher than `/invoices/page.tsx` and `/customers/page.tsx` in the file system, it's also applied to those pages.

We can change this with Route Groups. Create a new folder called `/(overview)` inside the dashboard folder. Then, move your `loading.tsx` and `page.tsx` files inside the folder:

Route groups allow you to organize files into logical groups without affecting the URL path structure. When you create a new folder using parentheses `()`, the name won't be included in the URL path. So `/dashboard/(overview)/page.tsx` becomes `/dashboard`.

### Suspense

Suspense allows you to defer rendering parts of your application until some condition is met (e.g. data is loaded). You can wrap your dynamic components in Suspense. Then, pass it a fallback component to show while the dynamic component loads.

To do so, you'll need to move the data fetch to the component.

```tsx
<Suspense fallback={<RevenueChartSkeleton />}>
  <RevenueChart />
</Suspense>
```

## Partial Prerendering

In Next.js 14, there is a preview of a new rendering model called Partial Prerendering. Partial Prerendering is an experimental feature that allows you to render a route with a static loading shell, while keeping some parts dynamic. In other words, you can isolate the dynamic parts of a route.

When a user visits a route:

- A static route shell is served, this makes the initial load fast.
- The shell leaves holes where dynamic content will load in async.
- The async holes are loaded in parallel, reducing the overall load time of the page.

The great thing about Partial Prerendering is that you don't need to change your code to use it. As long as you're using Suspense to wrap the dynamic parts of your route, Next.js will know which parts of your route are static and which are dynamic.

Note: To learn more about how Partial Prerendering can be configured, see the [Partial Prerendering (experimental) documentation](https://nextjs.org/docs/app/api-reference/next-config-js/partial-prerendering) or try the [Partial Prerendering template and demo](https://vercel.com/templates/next.js/partial-prerendering-nextjs). It's important to note that this feature is experimental and not yet ready for production deployment.

## Search & Pagination

### Search

Next.js client hooks to implement search functionality /w URL search params iso client state:

- **useSearchParams** - Allows you to access the parameters of the current URL. For example, the search params for this URL /dashboard/invoices?page=1&query=pending would look like this: {page: '1', query: 'pending'}.
- **usePathname** - Lets you read the current URL's pathname. For example, for the route /dashboard/invoices, usePathname would return '/dashboard/invoices'.
- **useRouter** - Enables navigation between routes within client components programmatically. There are multiple methods you can use.

```tsx
export default function Search({ placeholder }: { placeholder: string }) {
  const searchParams = useSearchParams();
  const pathname = usePathname();
  const { replace } = useRouter();

  function handleSearch(term: string) {
    const params = new URLSearchParams(searchParams);
    if (term) {
      params.set('query', term);
    } else {
      params.delete('query');
    }
    replace(`${pathname}?${params.toString()}`);
  }

  return (
    <div className="relative flex flex-1 flex-shrink-0">
      <label htmlFor="search" className="sr-only">
        Search
      </label>
      <input
        className="peer block w-full rounded-md border border-gray-200 py-[9px] pl-10 text-sm outline-2 placeholder:text-gray-500"
        placeholder={placeholder}
        onChange={(e) => {
          handleSearch(e.target.value);
        }}
        defaultValue={searchParams.get('query')?.toString()}
      />
      <MagnifyingGlassIcon className="absolute left-3 top-1/2 h-[18px] w-[18px] -translate-y-1/2 text-gray-500 peer-focus:text-gray-900" />
    </div>
  );
}
```

_defaultValue vs. value / Controlled vs. Uncontrolled
If you're using state to manage the value of an input, you'd use the value attribute to make it a controlled component. This means React would manage the input's state.
However, since you're not using state, you can use defaultValue. This means the native input will manage its own state. This is okay since you're saving the search query to the URL instead of state._

_/app/dashboard/invoices/page.tsx:_

```tsx
export default async function Page({
  searchParams,
}: {
  searchParams?: {
    query?: string;
    page?: string;
  };
}) {
  const query = searchParams?.query || '';
  const currentPage = Number(searchParams?.page) || 1;

  return (
    <div className="w-full">
      <div className="flex w-full items-center justify-between">
        <h1 className={`${lusitana.className} text-2xl`}>Invoices</h1>
      </div>
      <div className="mt-4 flex items-center justify-between gap-2 md:mt-8">
        <Search placeholder="Search invoices..." />
        <CreateInvoice />
      </div>
      <Suspense key={query + currentPage} fallback={<InvoicesTableSkeleton />}>
        <Table query={query} currentPage={currentPage} />
      </Suspense>
    </div>
  );
}
```

_/app/dashboard/invoices/page.tsx:_

```tsx
export default async function InvoicesTable({
  query,
  currentPage,
}: {
  query: string;
  currentPage: number;
}) {
  const invoices = await fetchFilteredInvoices(query, currentPage);
  // ...
}
```

### Debouncing

> npm i use-debounce

```tsx
const handleSearch = useDebouncedCallback((term) => {
  const params = new URLSearchParams(searchParams);
  if (term) {
    params.set('query', term);
  } else {
    params.delete('query');
  }
  replace(`${pathname}?${params.toString()}`);
}, 300);
```

### Pagination

- createPageURL creates an instance of the current search parameters.
- Then, it updates the "page" parameter to the provided page number.
- Finally, it constructs the full URL using the pathname and updated search parameters.

```tsx
export default function Pagination({ totalPages }: { totalPages: number }) {
  const pathname = usePathname();
  const searchParams = useSearchParams();
  const currentPage = Number(searchParams.get('page')) || 1;

  const createPageURL = (pageNumber: number | string) => {
    const params = new URLSearchParams(searchParams);
    params.set('page', pageNumber.toString());
    return `${pathname}?${params.toString()}`;
  };

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
