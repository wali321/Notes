# Notes
My personal Next.js notes and references.

---

# Basic Product Page with API

## Fetching data from an external API

```tsx
export default async function Page() {
  const response = await fetch("https://dummyjson.com/products");
  const data = await response.json();

  return (
    <main>
      {data.products.map((product: any) => (
        <div key={product.id}>
          <h2>{product.title}</h2>
          <p>${product.price}</p>
        </div>
      ))}
    </main>
  );
}
```
#the final 

```tsx

export default async function Page() {
  const response = await fetch("https://dummyjson.com/products");
  const data = await response.json();

  return (
    <section className="py-24">
      <div className="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
        <h2 className="font-manrope font-bold text-4xl text-black mb-8 max-lg:text-center">
          Product list
        </h2>

        <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-8">
          {data.products.map((product: any) => (
            <a
              key={product.id}
              href="#"
              className="mx-auto sm:mr-0 group cursor-pointer lg:mx-auto bg-white transition-all duration-500"
            >
              <div>
                <img
                  src={product.thumbnail}
                  alt={product.title}
                  className="w-full aspect-square rounded-2xl object-cover"
                />
              </div>

              <div className="mt-5">
                <div className="flex items-center justify-between">
                  <h6 className="font-semibold text-xl leading-8 text-black transition-all duration-500 group-hover:text-indigo-600">
                    {product.title}
                  </h6>

                  <h6 className="font-semibold text-xl leading-8 text-indigo-600">
                    ${product.price}
                  </h6>
                </div>
              </div>
            </a>
          ))}
        </div>
      </div>
    </section>
  );
}
```
