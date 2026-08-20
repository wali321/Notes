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
# The final 

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
# details page
```tsx
interface PageProps {
  params: Promise<{ productid: string }>;
}

export default async function ProductDetailPage({ params }: PageProps) {
  // Destructure productid (matches your folder name [productid])
  const { productid } = await params;

  try {
    const response = await fetch(`https://dummyjson.com/products/${productid}`);

    if (!response.ok) {
      return (
        <div className="p-8 text-center text-red-500">
          Product #{productid} not found.
        </div>
      );
    }

    const product = await response.json();

    return (
      <section className="py-24 max-w-4xl mx-auto px-4">
        <div className="grid grid-cols-1 md:grid-cols-2 gap-8">
          <img
            src={product.thumbnail}
            alt={product.title}
            className="w-full aspect-square rounded-2xl object-cover bg-gray-100"
          />
          <div className="flex flex-col justify-center">
            <h1 className="text-3xl font-bold mb-4">{product.title}</h1>
            <p className="text-indigo-600 text-2xl font-semibold mb-4">
              ${product.price}
            </p>
            <p className="text-gray-600 mb-6">{product.description}</p>
            <span className="text-sm text-gray-500">
              Category: <strong className="capitalize">{product.category}</strong>
            </span>
          </div>
        </div>
      </section>
    );
  } catch (error) {
    return (
      <div className="p-8 text-center text-red-500">
        Failed to load product.
      </div>
    );
  }
}
```
