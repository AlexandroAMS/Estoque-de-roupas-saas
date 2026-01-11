React
// services/api.ts
const API_URL = "http://localhost:8000";

export const api = {
  // Produtos
  getProducts: async () => {
    const res = await fetch(`${API_URL}/products`);
    return res.json();
  },
  createProduct: async (data: any) => {
    const res = await fetch(`${API_URL}/products`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data)
    });
    return res.json();
  },
  updateProduct: async (id: string, data: any) => {
    const res = await fetch(`${API_URL}/products/${id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data)
    });
    return res.json();
  },
  deleteProduct: async (id: string) => {
    await fetch(`${API_URL}/products/${id}`, { method: 'DELETE' });
  },

  // Vendas
  getSales: async () => {
    const res = await fetch(`${API_URL}/sales`);
    return res.json();
  },
  createSale: async (data: any) => {
    const res = await fetch(`${API_URL}/sales`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data)
    });
    return res.json();
  }
};
