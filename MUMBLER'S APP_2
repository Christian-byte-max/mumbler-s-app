import streamlit as st
import pandas as pd
from datetime import datetime, timedelta
import random

# Configuración de la página
st.set_page_config(
    page_title="Mumbler's - Gestión",
    page_icon="🏃‍♂️",
    layout="wide"
)

class SistemaInventario:
    def __init__(self):
        self.productos = [
            {
                'id': 1, 'nombre': 'Mumbler\'s Energy', 'categoria': 'Energía',
                'precio': 2.50, 'costo': 1.20, 'stock': 150, 'stock_minimo': 20,
                'proveedor': 'NutriSport'
            },
            {
                'id': 2, 'nombre': 'Mumbler\'s Hydration', 'categoria': 'Hidratación',
                'precio': 2.20, 'costo': 1.00, 'stock': 200, 'stock_minimo': 30,
                'proveedor': 'AquaPure'
            }
        ]
        self.ventas = self.generar_ventas_historicas()

    def generar_ventas_historicas(self):
        ventas = []
        for i in range(50):
            producto = random.choice(self.productos)
            ventas.append({
                'id': i + 1,
                'producto_id': producto['id'],
                'producto_nombre': producto['nombre'],
                'cantidad': random.randint(1, 5),
                'precio_unitario': producto['precio'],
                'total': random.randint(1, 5) * producto['precio'],
                'fecha': datetime.now() - timedelta(days=random.randint(0, 30)),
                'vendedor': random.choice(['Ana', 'Carlos', 'María'])
            })
        return ventas

def main():
    st.title("🏃‍♂️ Mumbler's - Sistema de Gestión")
    
    # Inicializar sistema
    if 'inventario' not in st.session_state:
        st.session_state.inventario = SistemaInventario()
    
    sistema = st.session_state.inventario
    
    # Sidebar
    with st.sidebar:
        st.header("Navegación")
        menu = st.radio("Selecciona una opción:", 
                       ["Dashboard", "Inventario", "Ventas"])
    
    # Contenido principal
    if menu == "Dashboard":
        mostrar_dashboard(sistema)
    elif menu == "Inventario":
        mostrar_inventario(sistema)
    elif menu == "Ventas":
        mostrar_ventas(sistema)

def mostrar_dashboard(sistema):
    st.header("📊 Dashboard")
    
    # Métricas
    col1, col2, col3 = st.columns(3)
    
    with col1:
        st.metric("Total Productos", len(sistema.productos))
    with col2:
        st.metric("Total Ventas", len(sistema.ventas))
    with col3:
        ingresos = sum(v['total'] for v in sistema.ventas)
        st.metric("Ingresos Totales", f"€{ingresos:,.2f}")
    
    # Tabla de productos
    st.subheader("📦 Productos en Inventario")
    df_productos = pd.DataFrame(sistema.productos)
    st.dataframe(df_productos[['nombre', 'categoria', 'precio', 'stock']])
    
    # Últimas ventas
    st.subheader("💰 Últimas Ventas")
    if sistema.ventas:
        df_ventas = pd.DataFrame(sistema.ventas[-10:])  # Últimas 10 ventas
        st.dataframe(df_ventas[['producto_nombre', 'cantidad', 'total', 'fecha', 'vendedor']])

def mostrar_inventario(sistema):
    st.header("📦 Gestión de Inventario")
    
    # Lista de productos
    st.subheader("Lista de Productos")
    for producto in sistema.productos:
        with st.expander(f"{producto['nombre']} - Stock: {producto['stock']}"):
            st.write(f"**Categoría:** {producto['categoria']}")
            st.write(f"**Precio:** €{producto['precio']}")
            st.write(f"**Proveedor:** {producto['proveedor']}")
            
            # Ajustar stock
            col1, col2 = st.columns(2)
            with col1:
                nueva_cantidad = st.number_input(f"Ajustar stock {producto['nombre']}", 
                                               value=producto['stock'], key=f"stock_{producto['id']}")
            with col2:
                if st.button(f"Actualizar {producto['nombre']}", key=f"btn_{producto['id']}"):
                    producto['stock'] = nueva_cantidad
                    st.success("Stock actualizado!")
                    st.rerun()

def mostrar_ventas(sistema):
    st.header("💰 Registrar Venta")
    
    with st.form("nueva_venta"):
        st.subheader("Nueva Venta")
        
        # Seleccionar producto
        producto_opciones = [f"{p['nombre']} (Stock: {p['stock']})" for p in sistema.productos]
        producto_seleccionado = st.selectbox("Producto", producto_opciones)
        
        # Obtener ID del producto seleccionado
        producto_idx = producto_opciones.index(producto_seleccionado)
        producto = sistema.productos[producto_idx]
        
        cantidad = st.number_input("Cantidad", min_value=1, max_value=producto['stock'])
        vendedor = st.selectbox("Vendedor", ["Ana", "Carlos", "María", "David"])
        
        total = cantidad * producto['precio']
        st.metric("Total a cobrar", f"€{total:.2f}")
        
        if st.form_submit_button("Registrar Venta"):
            nueva_venta = {
                'producto_id': producto['id'],
                'producto_nombre': producto['nombre'],
                'cantidad': cantidad,
                'precio_unitario': producto['precio'],
                'total': total,
                'vendedor': vendedor,
                'fecha': datetime.now()
            }
            sistema.ventas.append(nueva_venta)
            producto['stock'] -= cantidad
            st.success("✅ Venta registrada exitosamente!")
            st.balloons()

if __name__ == "__main__":
    main()
