from fastapi import FastAPI, HTTPException, Depends
from typing import List, Optional
from pydantic import BaseModel
from datetime import datetime
from uuid import uuid4

app = FastAPI(title="VestiControl API")

# --- Modelos de Dados (Schemas) ---

class Product(BaseModel):
    id: Optional[str] = None
    name: str
    sku: str
    category: str
    color: Optional[str] = None
    size: Optional[str] = None
    price: float
    quantity: int
    minStock: int = 5
    imageUrl: Optional[str] = None
    tenantId: str

class SaleItem(BaseModel):
    productId: str
    productName: str
    quantity: int
    unitPrice: float
    subtotal: float

class Sale(BaseModel):
    id: Optional[str] = None
    saleNumber: str
    date: datetime
    items: List[SaleItem]
    paymentMethod: str
    total: float
    customerName: Optional[str] = None
    tenantId: str

# --- Banco de Dados Simulado (Em Memória) ---
# Em produção, substitua por SQLAlchemy + PostgreSQL
db_products = []
db_sales = []

# --- Rotas de Produtos ---

@app.get("/products", response_model=List[Product])
def list_products():
    return db_products

@app.post("/products", response_model=Product)
def create_product(product: Product):
    product.id = str(uuid4())
    db_products.append(product)
    return product

@app.put("/products/{product_id}", response_model=Product)
def update_product(product_id: str, updated_product: Product):
    for index, p in enumerate(db_products):
        if p.id == product_id:
            updated_product.id = product_id
            db_products[index] = updated_product
            return updated_product
    raise HTTPException(status_code=404, detail="Produto não encontrado")

@app.delete("/products/{product_id}")
def delete_product(product_id: str):
    global db_products
    db_products = [p for p in db_products if p.id != product_id]
    return {"message": "Produto deletado"}

# --- Rotas de Vendas ---

@app.get("/sales", response_model=List[Sale])
def list_sales():
    return db_sales

@app.post("/sales", response_model=Sale)
def create_sale(sale: Sale):
    sale.id = str(uuid4())
    # Atualizar estoque
    for item in sale.items:
        for product in db_products:
            if product.id == item.productId:
                product.quantity -= item.quantity
                if product.quantity < 0: product.quantity = 0
    db_sales.append(sale)
    return sale

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
