import streamlit as st
import pandas as pd
import datetime
import json
from typing import Dict, List, Any
import random

class MumblerBot:
    def __init__(self):
        self.responses = {
            "saludo": [
                "¡Hola! Soy el asistente virtual de Mumbler's. ¿En qué puedo ayudarte hoy?",
                "¡Bienvenido a Mumbler's! Estoy aquí para resolver tus dudas sobre nuestras bebidas isotónicas.",
                "¡Hola! Gracias por contactar con Mumbler's. ¿Cómo puedo asistirte?"
            ],
            "despedida": [
                "¡Gracias por contactar con Mumbler's! Espero haberte ayudado.",
                "¡Que tengas un excelente día! Recuerda mantenerte hidratado con Mumbler's.",
                "¡Hasta pronto! No dudes en contactarnos si necesitas más ayuda."
            ],
            "productos": [
                "En Mumbler's ofrecemos una variedad de bebidas isotónicas: \n• Mumbler's Energy (500ml) \n• Mumbler's Hydration (750ml) \n• Mumbler's Pro (1L) \n• Mumbler's Zero Sugar (500ml)",
                "Nuestros productos incluyen bebidas isotónicas para diferentes necesidades: recuperación deportiva, hidratación diaria y energía instantánea.",
                "Tenemos 4 líneas principales: Energy, Hydration, Pro y Zero Sugar. ¿Te interesa alguna en particular?"
            ],
            "ingredientes": [
                "Nuestras bebidas contienen electrolitos esenciales, vitaminas B y C, y carbohidratos de rápida absorción.",
                "Los ingredientes varían por producto, pero todos incluyen sodio, potasio, magnesio y vitaminas. Sin conservantes artificiales.",
                "Usamos ingredientes naturales y formulaciones científicas para optimizar tu hidratación y rendimiento."
            ],
            "pedidos": [
                "Puedes realizar pedidos en nuestra página web en la sección 'Comprar' o llamando al 900-123-456.",
                "Los pedidos online tienen envío gratuito para compras superiores a €25. Trabajamos con entregas en 24-48h.",
                "Puedes hacer tu pedido en www.mumblers.com/comprar o visitando nuestros puntos de venta autorizados."
            ],
            "envio": [
                "Realizamos envíos a toda España en 24-48 horas laborables. Envío gratuito para pedidos >€25.",
                "El costo de envío es €4.90 para pedidos menores a €25. Seguimiento disponible por email.",
                "Entregamos de lunes a viernes. Puedes elegir franja horaria en el proceso de compra."
            ],
            "devoluciones": [
                "Aceptamos devoluciones en 30 días si el producto está sin abrir. Contacta con servicio.cliente@mumblers.com",
                "Para devoluciones, necesitamos el número de pedido y el motivo. Reembolsamos en 5-7 días hábiles.",
                "Política de devoluciones: 30 días para productos sellados. Proceso iniciado por email."
            ],
            "problemas_tecnicos": [
                "Si tienes problemas con la web, limpia la caché del navegador o prueba en modo incógnito.",
                "Para problemas técnicos, contacta a soporte.tecnico@mumblers.com con capturas del error.",
                "Nuestro equipo técnico resolverá cualquier incidencia. ¿Podrías describir el problema específico?"
            ],
            "contacto": [
                "📞 Teléfono: 900-123-456 \n📧 Email: info@mumblers.com \n📍 Dirección: Av. Deportiva 123, Madrid",
                "Puedes contactarnos por: \n• Teléfono: 900-123-456 (L-V 9:00-18:00) \n• Email: info@mumblers.com \n• Formulario web",
                "Estamos disponibles de lunes a viernes de 9:00 a 18:00. Teléfono: 900-123-456"
            ]
        }
        
        self.problemas_keywords = {
            "pagina web": "problemas_tecnicos",
            "error": "problemas_tecnicos", 
            "no funciona": "problemas_tecnicos",
            "producto": "productos",
            "bebida": "productos",
            "ingrediente": "ingredientes",
            "composición": "ingredientes",
            "comprar": "pedidos",
            "pedido": "pedidos",
            "envío": "envio",
            "entrega": "envio",
            "devolución": "devoluciones",
            "reembolso": "devoluciones",
            "contactar": "contacto",
            "teléfono": "contacto",
            "email": "contacto"
        }

    def clasificar_consulta(self, mensaje: str) -> str:
        mensaje = mensaje.lower()
        
        # Detectar saludos
        if any(palabra in mensaje for palabra in ["hola", "buenas", "hey", "hi"]):
            return "saludo"
            
        # Detectar despedidas
        if any(palabra in mensaje for palabra in ["adiós", "chao", "gracias", "hasta luego"]):
            return "despedida"
        
        # Buscar keywords específicos
        for keyword, categoria in self.problemas_keywords.items():
            if keyword in mensaje:
                return categoria
                
        return "general"

    def responder(self, mensaje: str) -> str:
        categoria = self.clasificar_consulta(mensaje)
        
        if categoria in self.responses:
            return random.choice(self.responses[categoria])
        else:
            return "Entiendo tu consulta. Para darte la mejor respuesta, por favor contacta con nuestro equipo humano en servicio.cliente@mumblers.com o llama al 900-123-456."

class SistemaGestionClientes:
    def __init__(self):
        self.tickets = []
        self.bot = MumblerBot()
        
    def crear_ticket(self, nombre: str, email: str, asunto: str, descripcion: str, prioridad: str = "media"):
        ticket = {
            "id": len(self.tickets) + 1,
            "nombre": nombre,
            "email": email,
            "asunto": asunto,
            "descripcion": descripcion,
            "prioridad": prioridad,
            "estado": "Abierto",
            "fecha_creacion": datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
            "respuestas": []
        }
        self.tickets.append(ticket)
        return ticket["id"]
    
    def agregar_respuesta(self, ticket_id: int, respuesta: str, es_cliente: bool = False):
        for ticket in self.tickets:
            if ticket["id"] == ticket_id:
                ticket["respuestas"].append({
                    "mensaje": respuesta,
                    "es_cliente": es_cliente,
                    "fecha": datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
                })
                break

def main():
    st.set_page_config(
        page_title="Mumbler's - Atención al Cliente",
        page_icon="🏃‍♂️",
        layout="wide"
    )
    
    # Inicializar sistema
    if 'sistema' not in st.session_state:
        st.session_state.sistema = SistemaGestionClientes()
    if 'chat_history' not in st.session_state:
        st.session_state.chat_history = []
    if 'mostrar_formulario' not in st.session_state:
        st.session_state.mostrar_formulario = False
    
    sistema = st.session_state.sistema
    
    # Header
    st.markdown("""
    <style>
    .main-header {
        font-size: 2.5rem;
        color: #2E86AB;
        text-align: center;
        margin-bottom: 2rem;
    }
    .bot-message {
        background-color: #F0F7FF;
        padding: 1rem;
        border-radius: 15px;
        margin: 0.5rem 0;
        border-left: 4px solid #2E86AB;
    }
    .user-message {
        background-color: #E8F5E8;
        padding: 1rem;
        border-radius: 15px;
        margin: 0.5rem 0;
        border-left: 4px solid #4CAF50;
    }
    </style>
    """, unsafe_allow_html=True)
    
    st.markdown('<h1 class="main-header">🏃‍♂️ Mumbler\'s - Centro de Atención al Cliente</h1>', unsafe_allow_html=True)
    
    # Sidebar para gestión
    with st.sidebar:
        st.header("📊 Panel de Gestión")
        
        if st.button("📝 Nuevo Ticket de Soporte"):
            st.session_state.mostrar_formulario = True
            
        if st.button("🗂️ Ver Tickets Existentes"):
            st.session_state.mostrar_formulario = False
            
        st.markdown("---")
        st.subheader("Estadísticas")
        st.metric("Tickets Activos", len([t for t in sistema.tickets if t["estado"] == "Abierto"]))
        st.metric("Total Tickets", len(sistema.tickets))
        
        # Quick actions
        st.markdown("---")
        st.subheader("Acciones Rápidas")
        if st.button("🔄 Actualizar"):
            st.rerun()
    
    # Área principal
    col1, col2 = st.columns([2, 1])
    
    with col1:
        st.subheader("💬 Chat con MumblerBot")
        
        # Historial del chat
        chat_container = st.container()
        with chat_container:
            for mensaje in st.session_state.chat_history:
                if mensaje["tipo"] == "bot":
                    st.markdown(f'<div class="bot-message"><strong>MumblerBot:</strong> {mensaje["contenido"]}</div>', unsafe_allow_html=True)
                else:
                    st.markdown(f'<div class="user-message"><strong>Tú:</strong> {mensaje["contenido"]}</div>', unsafe_allow_html=True)
        
        # Input del usuario
        with st.form("chat_form", clear_on_submit=True):
            user_input = st.text_input("Escribe tu mensaje:", placeholder="¿En qué puedo ayudarte?")
            enviado = st.form_submit_button("Enviar")
            
            if enviado and user_input:
                # Agregar mensaje del usuario al historial
                st.session_state.chat_history.append({
                    "tipo": "usuario",
                    "contenido": user_input
                })
                
                # Obtener respuesta del bot
                respuesta = sistema.bot.responder(user_input)
                st.session_state.chat_history.append({
                    "tipo": "bot", 
                    "contenido": respuesta
                })
                
                st.rerun()
    
    with col2:
        if st.session_state.mostrar_formulario:
            st.subheader("📝 Crear Ticket de Soporte")
            
            with st.form("ticket_form"):
                nombre = st.text_input("Nombre completo*")
                email = st.text_input("Email*")
                asunto = st.selectbox("Tipo de consulta*", [
                    "Problema con la web",
                    "Consulta sobre productos", 
                    "Problema con pedido",
                    "Devolución/reembolso",
                    "Otro"
                ])
                descripcion = st.text_area("Descripción detallada*", height=150)
                prioridad = st.selectbox("Prioridad", ["Baja", "Media", "Alta"])
                
                if st.form_submit_button("Crear Ticket"):
                    if nombre and email and descripcion:
                        ticket_id = sistema.crear_ticket(nombre, email, asunto, descripcion, prioridad)
                        st.success(f"✅ Ticket #{ticket_id} creado exitosamente!")
                        st.info("Nuestro equipo te contactará en 24-48 horas.")
                    else:
                        st.error("Por favor completa todos los campos obligatorios.")
        else:
            st.subheader("🗂️ Tickets Existentes")
            
            if sistema.tickets:
                for ticket in reversed(sistema.tickets[-5:]):  # Mostrar últimos 5 tickets
                    with st.expander(f"Ticket #{ticket['id']} - {ticket['asunto']}"):
                        st.write(f"**Cliente:** {ticket['nombre']}")
                        st.write(f"**Email:** {ticket['email']}")
                        st.write(f"**Estado:** {ticket['estado']}")
                        st.write(f"**Prioridad:** {ticket['prioridad']}")
                        st.write(f"**Descripción:** {ticket['descripcion']}")
                        
                        if st.button(f"Cerrar Ticket #{ticket['id']}"):
                            ticket["estado"] = "Cerrado"
                            st.success("Ticket cerrado")
                            st.rerun()
            else:
                st.info("No hay tickets creados aún.")

    # Footer informativo
    st.markdown("---")
    col1, col2, col3 = st.columns(3)
    
    with col1:
        st.subheader("📞 Contacto Directo")
        st.write("**Teléfono:** 900-123-456")
        st.write("**Email:** info@mumblers.com")
        st.write("**Horario:** L-V 9:00-18:00")
    
    with col2:
        st.subheader("🚚 Envíos y Devoluciones")
        st.write("• Envío gratis >€25")
        st.write("• Entrega 24-48h")
        st.write("• Devoluciones en 30 días")
    
    with col3:
        st.subheader("🔧 Soporte Técnico")
        st.write("• Problemas web")
        st.write("• Errores de pedido")
        st.write("• Facturación")

if __name__ == "__main__":
    main()
📋 Código completo para copiar - App 2: Gestión de Inventario
python
# app_inventario.py
import streamlit as st
import pandas as pd
import plotly.express as px
import plotly.graph_objects as go
from datetime import datetime, timedelta
import random
import json
import io

class SistemaInventario:
    def __init__(self):
        self.productos = [
            {
                'id': 1,
                'nombre': 'Mumbler\'s Energy',
                'categoria': 'Energía',
                'precio': 2.50,
                'costo': 1.20,
                'stock': 150,
                'stock_minimo': 20,
                'proveedor': 'NutriSport',
                'lote': 'LOTE-2024-ENE'
            },
            {
                'id': 2,
                'nombre': 'Mumbler\'s Hydration',
                'categoria': 'Hidratación',
                'precio': 2.20,
                'costo': 1.00,
                'stock': 200,
                'stock_minimo': 30,
                'proveedor': 'AquaPure',
                'lote': 'LOTE-2024-FEB'
            },
            {
                'id': 3,
                'nombre': 'Mumbler\'s Pro',
                'categoria': 'Profesional',
                'precio': 3.00,
                'costo': 1.50,
                'stock': 80,
                'stock_minimo': 15,
                'proveedor': 'ProSupply',
                'lote': 'LOTE-2024-MAR'
            },
            {
                'id': 4,
                'nombre': 'Mumbler\'s Zero Sugar',
                'categoria': 'Zero Sugar',
                'precio': 2.80,
                'costo': 1.30,
                'stock': 120,
                'stock_minimo': 25,
                'proveedor': 'SugarFree Co',
                'lote': 'LOTE-2024-ABR'
            }
        ]
        
        self.ventas = self.generar_ventas_historicas()
        self.pedidos_pendientes = []

    def generar_ventas_historicas(self):
        ventas = []
        fecha_inicio = datetime.now() - timedelta(days=90)
        
        for i in range(200):
            producto = random.choice(self.productos)
            cantidad = random.randint(1, 10)
            fecha = fecha_inicio + timedelta(days=random.randint(0, 90))
            
            venta = {
                'id': i + 1,
                'producto_id': producto['id'],
                'producto_nombre': producto['nombre'],
                'cantidad': cantidad,
                'precio_unitario': producto['precio'],
                'total': cantidad * producto['precio'],
                'fecha': fecha,
                'vendedor': random.choice(['Ana', 'Carlos', 'María', 'David']),
                'cliente': f'Cliente_{random.randint(1000, 9999)}'
            }
            ventas.append(venta)
        
        return sorted(ventas, key=lambda x: x['fecha'])

    def agregar_producto(self, producto_data):
        nuevo_id = max([p['id'] for p in self.productos]) + 1
        producto_data['id'] = nuevo_id
        self.productos.append(producto_data)
        return nuevo_id

    def actualizar_stock(self, producto_id, cantidad):
        for producto in self.productos:
            if producto['id'] == producto_id:
                producto['stock'] += cantidad
                return True
        return False

    def registrar_venta(self, venta_data):
        venta_data['id'] = len(self.ventas) + 1
        venta_data['fecha'] = datetime.now()
        self.ventas.append(venta_data)
        
        # Actualizar stock
        for producto in self.productos:
            if producto['id'] == venta_data['producto_id']:
                producto['stock'] -= venta_data['cantidad']
                break

    def obtener_alertas_stock(self):
        alertas = []
        for producto in self.productos:
            if producto['stock'] <= producto['stock_minimo']:
                alertas.append({
                    'producto': producto['nombre'],
                    'stock_actual': producto['stock'],
                    'stock_minimo': producto['stock_minimo'],
                    'nivel': 'CRÍTICO' if producto['stock'] == 0 else 'BAJO'
                })
        return alertas

    def obtener_estadisticas_ventas(self):
        df_ventas = pd.DataFrame(self.ventas)
        if df_ventas.empty:
            return {}
        
        stats = {
            'total_ventas': len(self.ventas),
            'ingresos_totales': df_ventas['total'].sum(),
            'ventas_mes_actual': len([v for v in self.ventas if v['fecha'].month == datetime.now().month]),
            'producto_mas_vendido': df_ventas['producto_nombre'].mode()[0] if not df_ventas.empty else 'N/A',
            'mejor_vendedor': df_ventas['vendedor'].mode()[0] if not df_ventas.empty else 'N/A'
        }
        return stats

def main():
    st.set_page_config(
        page_title="Mumbler's - Gestión de Inventario",
        page_icon="📊",
        layout="wide",
        initial_sidebar_state="expanded"
    )

    # CSS personalizado
    st.markdown("""
    <style>
    .main-header {
        font-size: 2.5rem;
        color: #2E86AB;
        text-align: center;
        margin-bottom: 2rem;
        background: linear-gradient(135deg, #2E86AB 0%, #A23B72 100%);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        font-weight: bold;
    }
    .metric-card {
        background-color: #f8f9fa;
        padding: 1rem;
        border-radius: 10px;
        border-left: 4px solid #2E86AB;
        margin: 0.5rem 0;
    }
    .alert-card {
        background-color: #fff3cd;
        padding: 1rem;
        border-radius: 10px;
        border-left: 4px solid #ffc107;
        margin: 0.5rem 0;
    }
    .critical-alert {
        background-color: #f8d7da;
        border-left: 4px solid #dc3545;
    }
    </style>
    """, unsafe_allow_html=True)

    # Inicializar sistema
    if 'inventario' not in st.session_state:
        st.session_state.inventario = SistemaInventario()

    sistema = st.session_state.inventario

    # Header
    st.markdown('<h1 class="main-header">🏃‍♂️ Mumbler\'s - Sistema de Gestión</h1>', unsafe_allow_html=True)

    # Sidebar
    with st.sidebar:
        st.image("https://via.placeholder.com/150x50/2E86AB/FFFFFF?text=Mumbler's", width=150)
        st.markdown("---")
        
        menu = st.radio(
            "Navegación",
            ["📊 Dashboard", "📦 Gestión de Inventario", "💰 Registrar Venta", "📈 Reportes", "⚙️ Configuración"]
        )
        
        st.markdown("---")
        st.subheader("Alertas Rápidas")
        alertas = sistema.obtener_alertas_stock()
        if alertas:
            for alerta in alertas[:3]:
                st.error(f"⚠️ {alerta['producto']}: {alerta['stock_actual']} unidades")
        else:
            st.success("✅ Stock en niveles normales")

    # Contenido principal basado en la selección del menú
    if menu == "📊 Dashboard":
        mostrar_dashboard(sistema)
    elif menu == "📦 Gestión de Inventario":
        mostrar_gestion_inventario(sistema)
    elif menu == "💰 Registrar Venta":
        mostrar_registro_venta(sistema)
    elif menu == "📈 Reportes":
        mostrar_reportes(sistema)
    elif menu == "⚙️ Configuración":
        mostrar_configuracion(sistema)

def mostrar_dashboard(sistema):
    col1, col2, col3, col4 = st.columns(4)
    
    stats = sistema.obtener_estadisticas_ventas()
    
    with col1:
        st.metric("Productos en Inventario", len(sistema.productos))
    with col2:
        st.metric("Ventas Totales", stats.get('total_ventas', 0))
    with col3:
        st.metric("Ingresos Totales", f"€{stats.get('ingresos_totales', 0):,.2f}")
    with col4:
        st.metric("Ventas Este Mes", stats.get('ventas_mes_actual', 0))
    
    st.markdown("---")
    
    # Gráficos principales
    col1, col2 = st.columns(2)
    
    with col1:
        st.subheader("📈 Ventas por Producto")
        df_ventas = pd.DataFrame(sistema.ventas)
        if not df_ventas.empty:
            ventas_por_producto = df_ventas.groupby('producto_nombre')['total'].sum().reset_index()
            fig = px.bar(ventas_por_producto, x='producto_nombre', y='total', 
                         color='producto_nombre', title="Ingresos por Producto")
            st.plotly_chart(fig, use_container_width=True)
        else:
            st.info("No hay datos de ventas para mostrar")
    
    with col2:
        st.subheader("📦 Niveles de Stock")
        df_productos = pd.DataFrame(sistema.productos)
        fig = px.bar(df_productos, x='nombre', y='stock', color='categoria',
                     title="Stock Actual por Producto")
        st.plotly_chart(fig, use_container_width=True)
    
    # Alertas de stock
    st.subheader("🚨 Alertas de Stock")
    alertas = sistema.obtener_alertas_stock()
    if alertas:
        for alerta in alertas:
            with st.container():
                col1, col2, col3 = st.columns([3, 1, 1])
                with col1:
                    st.write(f"**{alerta['producto']}**")
                with col2:
                    st.write(f"Stock: {alerta['stock_actual']}")
                with col3:
                    if alerta['nivel'] == 'CRÍTICO':
                        st.error("CRÍTICO")
                    else:
                        st.warning("BAJO")
                st.progress(alerta['stock_actual'] / (alerta['stock_minimo'] * 3))
    else:
        st.success("✅ Todos los productos tienen stock suficiente")

def mostrar_gestion_inventario(sistema):
    st.header("📦 Gestión de Inventario")
    
    col1, col2 = st.columns([2, 1])
    
    with col1:
        st.subheader("Lista de Productos")
        
        # Mostrar productos en tabla
        df_productos = pd.DataFrame(sistema.productos)
        st.dataframe(
            df_productos[['id', 'nombre', 'categoria', 'precio', 'stock', 'stock_minimo', 'proveedor']],
            use_container_width=True
        )
    
    with col2:
        st.subheader("Acciones Rápidas")
        
        # Ajustar stock
        with st.form("ajustar_stock"):
            st.write("**Ajustar Stock**")
            producto_id = st.selectbox(
                "Producto",
                options=[p['id'] for p in sistema.productos],
                format_func=lambda x: next(p['nombre'] for p in sistema.productos if p['id'] == x)
            )
            cantidad = st.number_input("Cantidad", min_value=-100, max_value=100, value=0)
            motivo = st.selectbox("Motivo", ["Ajuste inventario", "Devolución", "Daño", "Otro"])
            
            if st.form_submit_button("Aplicar Ajuste"):
                sistema.actualizar_stock(producto_id, cantidad)
                st.success(f"Stock actualizado correctamente")
                st.rerun()
        
        # Agregar nuevo producto
        with st.expander("➕ Agregar Producto"):
            with st.form("nuevo_producto"):
                nombre = st.text_input("Nombre del Producto")
                categoria = st.selectbox("Categoría", ["Energía", "Hidratación", "Profesional", "Zero Sugar"])
                precio = st.number_input("Precio de Venta", min_value=0.0, step=0.1)
                costo = st.number_input("Costo", min_value=0.0, step=0.1)
                stock = st.number_input("Stock Inicial", min_value=0)
                stock_minimo = st.number_input("Stock Mínimo", min_value=0)
                proveedor = st.text_input("Proveedor")
                
                if st.form_submit_button("Agregar Producto"):
                    nuevo_producto = {
                        'nombre': nombre,
                        'categoria': categoria,
                        'precio': precio,
                        'costo': costo,
                        'stock': stock,
                        'stock_minimo': stock_minimo,
                        'proveedor': proveedor,
                        'lote': f"LOTE-{datetime.now().strftime('%Y-%m')}"
                    }
                    sistema.agregar_producto(nuevo_producto)
                    st.success("Producto agregado correctamente")
                    st.rerun()

def mostrar_registro_venta(sistema):
    st.header("💰 Registrar Nueva Venta")
    
    col1, col2 = st.columns(2)
    
    with col1:
        with st.form("registro_venta"):
            st.subheader("Detalles de la Venta")
            
            # Seleccionar producto
            producto_seleccionado = st.selectbox(
                "Producto",
                options=sistema.productos,
                format_func=lambda x: f"{x['nombre']} - Stock: {x['stock']} - €{x['precio']}"
            )
            
            cantidad = st.number_input("Cantidad", min_value=1, max_value=producto_seleccionado['stock'], value=1)
            
            col_a, col_b = st.columns(2)
            with col_a:
                precio_unitario = st.number_input("Precio Unitario", value=float(producto_seleccionado['precio']), step=0.1)
            with col_b:
                total = cantidad * precio_unitario
                st.metric("Total", f"€{total:.2f}")
            
            vendedor = st.selectbox("Vendedor", ["Ana", 'Carlos', 'María', 'David', 'Otro'])
            cliente = st.text_input("Cliente (opcional)")
            notas = st.text_area("Notas adicionales")
            
            if st.form_submit_button("💳 Registrar Venta", use_container_width=True):
                venta_data = {
                    'producto_id': producto_seleccionado['id'],
                    'producto_nombre': producto_seleccionado['nombre'],
                    'cantidad': cantidad,
                    'precio_unitario': precio_unitario,
                    'total': total,
                    'vendedor': vendedor,
                    'cliente': cliente,
                    'notas': notas
                }
                
                sistema.registrar_venta(venta_data)
                st.success("✅ Venta registrada exitosamente!")
                st.balloons()
    
    with col2:
        st.subheader("Resumen de Productos Disponibles")
        
        for producto in sistema.productos:
            with st.container():
                col1, col2, col3 = st.columns([3, 1, 1])
                with col1:
                    st.write(f"**{producto['nombre']}**")
                with col2:
                    st.write(f"Stock: {producto['stock']}")
                with col3:
                    st.write(f"€{producto['precio']}")
                st.progress(min(producto['stock'] / 100, 1.0))

def mostrar_reportes(sistema):
    st.header("📈 Reportes y Análisis")
    
    # Filtros
    col1, col2, col3 = st.columns(3)
    with col1:
        fecha_inicio = st.date_input("Fecha Inicio", datetime.now() - timedelta(days=30))
    with col2:
        fecha_fin = st.date_input("Fecha Fin", datetime.now())
    with col3:
        producto_filtro = st.selectbox("Filtrar por Producto", ["Todos"] + [p['nombre'] for p in sistema.productos])
    
    # Filtrar ventas
    ventas_filtradas = [
        v for v in sistema.ventas 
        if fecha_inicio <= v['fecha'].date() <= fecha_fin
        and (producto_filtro == "Todos" or v['producto_nombre'] == producto_filtro)
    ]
    
    if not ventas_filtradas:
        st.warning("No hay datos para el período seleccionado")
        return
    
    df_ventas = pd.DataFrame(ventas_filtradas)
    
    # Métricas
    col1, col2, col3, col4 = st.columns(4)
    with col1:
        st.metric("Total Ventas", len(ventas_filtradas))
    with col2:
        st.metric("Ingresos Totales", f"€{df_ventas['total'].sum():,.2f}")
    with col3:
        st.metric("Productos Vendidos", df_ventas['cantidad'].sum())
    with col4:
        st.metric("Ticket Promedio", f"€{df_ventas['total'].mean():.2f}")
    
    # Gráficos
    col1, col2 = st.columns(2)
    
    with col1:
        st.subheader("Tendencia de Ventas")
        df_ventas['fecha_str'] = df_ventas['fecha'].dt.date
        ventas_diarias = df_ventas.groupby('fecha_str')['total'].sum().reset_index()
        fig = px.line(ventas_diarias, x='fecha_str', y='total', title="Ventas Diarias")
        st.plotly_chart(fig, use_container_width=True)
    
    with col2:
        st.subheader("Ventas por Vendedor")
        ventas_vendedor = df_ventas.groupby('vendedor')['total'].sum().reset_index()
        fig = px.pie(ventas_vendedor, values='total', names='vendedor', title="Distribución por Vendedor")
        st.plotly_chart(fig, use_container_width=True)
    
    # Tabla detallada
    st.subheader("Detalle de Ventas")
    st.dataframe(df_ventas, use_container_width=True)
    
    # Exportar datos
    st.download_button(
        label="📥 Exportar a CSV",
        data=df_ventas.to_csv(index=False).encode('utf-8'),
        file_name=f"reporte_ventas_{datetime.now().strftime('%Y%m%d')}.csv",
        mime="text/csv"
    )

def mostrar_configuracion(sistema):
    st.header("⚙️ Configuración del Sistema")
    
    tab1, tab2, tab3 = st.tabs(["General", "Productos", "Backup"])
    
    with tab1:
        st.subheader("Configuración General")
        
        col1, col2 = st.columns(2)
        with col1:
            st.number_input("Stock Mínimo Predeterminado", min_value=1, value=20)
            st.number_input("Margen Mínimo (%)", min_value=0, value=30)
        with col2:
            st.text_input("Email Notificaciones")
            st.text_input("Teléfono Contacto")
        
        if st.button("Guardar Configuración"):
            st.success("Configuración guardada correctamente")
    
    with tab2:
        st.subheader("Categorías de Productos")
        
        categorias = list(set(p['categoria'] for p in sistema.productos))
        for cat in categorias:
            st.write(f"• {cat}")
        
        nueva_categoria = st.text_input("Nueva Categoría")
        if st.button("Agregar Categoría") and nueva_categoria:
            st.success(f"Categoría '{nueva_categoria}' agregada")
    
    with tab3:
        st.subheader("Copia de Seguridad")
        
        col1, col2 = st.columns(2)
        
        with col1:
            st.info("Exportar Datos")
            if st.button("📤 Exportar Todo"):
                datos
