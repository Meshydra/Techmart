<script>
    import { onMount } from 'svelte';
    import { writable, derived } from 'svelte/store';
    
    // Create a writable store for the main product list
    const allProducts = writable([]);

    // Create a writable store for filtered products
    const filteredProducts = writable([]);

    let category = '';
    let minPrice = '';
    let maxPrice = '';

    // Load initial products and set the allProducts store
    onMount(async () => {
        const response = await fetch('https://dummyjson.com/products');
        if (response.ok) {
            const data = await response.json();
            allProducts.set(data.products);
        } else {
            console.error('Failed to fetch products');
        }
    });

    // Function to update filteredProducts
    function applyFilters() {
        const filter = {
            category: category,
            minPrice: minPrice,
            maxPrice: maxPrice
        };

        // Apply filters to the product list
        const updatedProducts = $allProducts.filter(product => {
            // Apply category filter if set
            if (filter.category && product.category !== filter.category) {
                return false;
            }

            // Apply price range filter if set
            if (
                (filter.minPrice && product.price < filter.minPrice) ||
                (filter.maxPrice && product.price > filter.maxPrice)
            ) {
                return false;
            }

            return true;
        });

        // Update the filteredProducts store with the filtered products
        filteredProducts.set(updatedProducts);
    }
</script>

<div class="p-6 bg-gradient-to-r shadow-lg">
    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        <input
            class="p-3 rounded-lg bg-gray-800 text-white focus:outline-none focus:ring-2 focus:ring-blue-500"
            bind:value={category}
            placeholder="Category"
        >
        <input
            class="p-3 rounded-lg bg-gray-800 text-white focus:outline-none focus:ring-2 focus:ring-blue-500"
            bind:value={minPrice}
            type="number"
            placeholder="Min Price"
        >
        <input
            class="p-3 rounded-lg bg-gray-800 text-white focus:outline-none focus:ring-2 focus:ring-blue-500"
            bind:value={maxPrice}
            type="number"
            placeholder="Max Price"
        >
    </div>
    <button
        class="mt-4 py-2 px-4 bg-blue-600 hover:bg-blue-700 text-white rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
        on:click={applyFilters}
    >
        Apply Filters
    </button>
</div>

<div class="container mx-auto py-8">
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
        {#each $filteredProducts as product (product.id)}
            <div class="shadow-lg rounded-lg overflow-hidden">
                <div class="p-4 rounded-t-lg">
                    <div class="h-48 w-full relative rounded-t-lg">
                        <img src={product.thumbnail} alt={product.title} class="object-contain h-full w-full rounded-t-lg" />
                    </div>
                    <h2 class="text-lg font-semibold mt-2">{product.title}</h2>
                    <p class="mt-2 text-gray-600">${product.price}</p>
                </div>
            </div>
        {/each}
    </div>
</div>
