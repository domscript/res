# Some code

I love JavaScript, but sometimes it can be time-consuming to find typos. That's why nowadays I mainly use TypeScript.

## User Snippets

My brain refuses to memorize stuff that I don't use much, so I'd like to create User Snippets like the one below for React Context:

**typescriptreact.json**

```json
 "TSX Context Provider (User Snippet)": {
    "prefix": "tsxcontextSE",
    "body": [
      "// fineName should be <Item>sProvider.tsx",
      "// Change <Item> to your, example: ProductsProvider.tsx\n",
      "import { createContext, ReactElement, useEffect, useState } from 'react';\n",
      "export type ${TM_FILENAME_BASE/(.*)(sProvider)/$1/}Type = {",
      "\tsku: string;",
      "\tname: string;",
      "\tprice: number;",
      "};\n",
      "const initState: ${TM_FILENAME_BASE/(.*)(sProvider)/$1/}Type[] = [];\n",
      "export type Use${TM_FILENAME_BASE/(.*)(Provider)/$1/}ContextType = { ${TM_FILENAME_BASE/(.*)(Provider)/${1:/downcase}/}: ${TM_FILENAME_BASE/(.*)(sProvider)/$1/}Type[] };\n",
      "const contextState: Use${TM_FILENAME_BASE/(.*)(Provider)/$1/}ContextType = { ${TM_FILENAME_BASE/(.*)(Provider)/${1:/downcase}/}: [] };\n",
      "const ${TM_FILENAME_BASE/(.*)(Provider)/$1/}Context = createContext<Use${TM_FILENAME_BASE/(.*)(Provider)/$1/}ContextType>(contextState);\n",
      "type ChildrenType = { children?: ReactElement | ReactElement[] };\n",
      "export const ${TM_FILENAME_BASE} = ({ children }: ChildrenType): ReactElement => {",
      "\tconst [${TM_FILENAME_BASE/(.*)(Provider)/${1:/downcase}/}, set${TM_FILENAME_BASE/(.*)(Provider)/$1/}] = useState<${TM_FILENAME_BASE/(.*)(sProvider)/$1/}Type[]>(initState);\n",
      "\tuseEffect(() => {",
      "\t\tconst fetch${TM_FILENAME_BASE/(.*)(Provider)/$1/} = async (): Promise<${TM_FILENAME_BASE/(.*)(sProvider)/$1/}Type[]> => {",
      "\t\t\treturn await fetch('http://localhost:3000/${TM_FILENAME_BASE/(.*)(Provider)/${1:/downcase}/}')",
      "\t\t\t\t.then((res) => res.json())",
      "\t\t\t\t.catch((err) => {",
      "\t\t\t\t\terr instanceof Error && console.log(err.message);",
      "\t\t\t\t});",
      "\t\t};\n",
      "\t\tfetch${TM_FILENAME_BASE/(.*)(Provider)/$1/}().then((${TM_FILENAME_BASE/(.*)(Provider)/${1:/downcase}/}) => set${TM_FILENAME_BASE/(.*)(Provider)/$1/}(${TM_FILENAME_BASE/(.*)(Provider)/${1:/downcase}/}));",
      "\t}, []);\n",
      "\treturn (",
      "\t\t<${TM_FILENAME_BASE/(.*)(Provider)/$1/}Context.Provider value={{ ${TM_FILENAME_BASE/(.*)(Provider)/${1:/downcase}/} }}>",
      "\t\t\t{children}",
      "\t\t</${TM_FILENAME_BASE/(.*)(Provider)/$1/}Context.Provider>",
      "\t);",
      "};\n",
      "export default ${TM_FILENAME_BASE/(.*)(Provider)/$1/}Context;"
    ],
    "description": "TSX Context Provider (items)"
  },
```

If I type `tsxcontextSE` and press `Enter` in `ProductsProvider.tsx` file, fileName: `ProductsProvider` or just its part is used 24 times, and I have a good start. This puts me in a good mood.

**ProductsProvider.tsx**

```tsx
// fineName should be <Item>sProvider.tsx
// Change <Item> to your, example: ProductsProvider.tsx

import { createContext, ReactElement, useEffect, useState } from "react";

export type ProductType = {
  sku: string;
  name: string;
  price: number;
};

const initState: ProductType[] = [];

export type UseProductsContextType = { products: ProductType[] };

const contextState: UseProductsContextType = { products: [] };

const ProductsContext = createContext<UseProductsContextType>(contextState);

type ChildrenType = { children?: ReactElement | ReactElement[] };

export const ProductsProvider = ({ children }: ChildrenType): ReactElement => {
  const [products, setProducts] = useState<ProductType[]>(initState);

  useEffect(() => {
    const fetchProducts = async (): Promise<ProductType[]> => {
      return await fetch("http://localhost:3000/products")
        .then((res) => res.json())
        .catch((err) => {
          err instanceof Error && console.log(err.message);
        });
    };

    fetchProducts().then((products) => setProducts(products));
  }, []);

  return (
    <ProductsContext.Provider value={{ products }}>
      {children}
    </ProductsContext.Provider>
  );
};

export default ProductsContext;
```

The code defines a React context called `ProductsContext` that provides a list of ProductType objects to its descendants. The `ProductsProvider` component is responsible for fetching the data from the server using fetch and making it available to the context.

The `ProductsProvider` component is set up to receive child components as props using the destructured children prop. The component fetches the product data from the server in the useEffect hook and sets the products state using the setProducts function. The products state is then passed as the value of the `ProductsContext.Provider`.

Overall, the code should provide a good foundation for managing product data in a React application. However, you may want to consider adding error handling and loading states to the component to provide a better user experience.

## I prefer to use Redux Toolkit, but it's still not well integrated with TypeScript. Therefore, I have to use extra code:

**utils.ts**

```ts
import {
  Await as RrdAwait,
  defer,
  LoaderFunctionArgs,
  ActionFunctionArgs,
  useActionData as useRrdActionData,
  useLoaderData as useRrdLoaderData,
} from "react-router-dom";

export interface EventsInt {
  title: string;
  image: string;
  description: string;
  date: Date;
  id: string;
}

export function useActionData<
  TLoader extends ReturnType<typeof deferredAction>
>() {
  return useRrdActionData() as ReturnType<TLoader>["data"];
}

export function deferredAction<TData extends Record<string, Response>>(
  dataFunc: (args: ActionFunctionArgs) => TData
) {
  return (args: ActionFunctionArgs) =>
    defer(dataFunc(args)) as Omit<ReturnType<typeof defer>, "data"> & {
      data: TData;
    };
}

export function useLoaderData<
  TLoader extends ReturnType<typeof deferredLoader>
>() {
  return useRrdLoaderData() as ReturnType<TLoader>["data"];
}

export function deferredLoader<
  TData extends Record<string, Promise<Array<EventsInt>>>
>(dataFunc: (args: LoaderFunctionArgs) => TData) {
  return (args: LoaderFunctionArgs) =>
    defer(dataFunc(args)) as Omit<ReturnType<typeof defer>, "data"> & {
      data: TData;
    };
}

export interface AwaitResolveRenderFunction<T> {
  (data: Awaited<T>): React.ReactElement;
}

export interface AwaitProps<T> {
  children: React.ReactNode | AwaitResolveRenderFunction<T>;
  errorElement?: React.ReactNode;
  resolve: Promise<T>;
}

export function Await<T>(props: AwaitProps<T>): JSX.Element {
  return RrdAwait(props);
}
```

The code provided in `utils.ts` file defines some utility functions and interfaces for use with `react-router-dom`. The `useActionData`, `deferredAction`, `useLoaderData`, and `deferredLoader` functions are designed to help create `actions` and `loaders` with `react-router-dom` that return data specific to this application. The `Await` component is a wrapper around the react-router-dom Await component, which enables to render a loading state or an error message while waiting for a promise to resolve. Overall, this code should help integrate redux-toolkit with react-router-dom in a type-safe manner.

## This is a simple component for rendering any list where you pass the rendering items and logic via render props.

The component only cares about the `id` property of each item. The EventsList component takes in a generic type T which extends ItemWithId, an interface with an `id` property. It then accepts an array of items of type T, and a render prop which takes an item of type T and returns a ReactNode. Inside the component, it maps over the items array and renders each item with the specified rendering logic.

**EventsList.tsx**

```tsx
import { ReactNode } from "react";
import classes from "./EventsList.module.css";

export interface ItemWithId {
  id: string;
}

interface EventsListProps<T extends ItemWithId> {
  items: T[];
  render: (item: T) => ReactNode;
}

export const EventsList = <T extends ItemWithId>({
  items,
  render,
}: EventsListProps<T>) => {
  return (
    <ul className={classes.list}>
      {items.map((item) => (
        <li key={item.id} className={classes.item}>
          {render(item)}
        </li>
      ))}
    </ul>
  );
};
```

## Combine all code above I have simple component:

Here TypeScript Happy in `strict mode`.

**Events.tsx**

```tsx
import { Suspense } from "react";
import { Link, json, defer } from "react-router-dom";
import { Await, useLoaderData } from "../utils";
import { EventsList } from "./EventsList";
import classes from "./Events.module.css";

export const Events = () => {
  const { events } = useLoaderData();
  return (
    <>
      <Suspense fallback={<p>Suspense</p>}>
        <Await resolve={events}>
          {(loadedEvents) => (
            <EventsList
              items={loadedEvents}
              render={(event) => (
                <Link to={`/events/${event.id}`}>
                  <img src={event.image} alt={event.title} />
                  <div className={classes.content}>
                    <h2>{event.title}</h2>
                    <time>{new Date(event.date).toLocaleDateString()}</time>
                  </div>
                </Link>
              )}
            />
          )}
        </Await>
      </Suspense>
    </>
  );
};

async function loadsEvents() {
  const response = await fetch("http://localhost:8080/events");

  if (!response.ok) {
    throw json({ message: "Could not fetch events." }, { status: 500 });
  } else {
    const { events } = await response.json();
    return events;
  }
}

export const loader = () => {
  return defer({
    events: loadsEvents(),
  });
};
```

The `Events` component is a container component that fetches a list of events and renders them using the `EventsList` component. It uses the `useLoaderData` hook to retrieve the events from the server and then renders them in a suspense component. The Await component waits for the events to load before rendering the `EventsList` component. The `EventsList` component takes in the loaded events and renders each event using the `render` prop.

The `loadsEvents` function is an asynchronous function that fetches events from the server. It throws an error if the response is not successful, or returns the events if it is. The `loader` function is a server-side function that asynchronously loads the data needed by the Events component. It returns an object with a single property `events`, which contains the loaded events.
