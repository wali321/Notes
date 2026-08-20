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
