# Notes
my notes for personal work


#  Basic product page with api
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
