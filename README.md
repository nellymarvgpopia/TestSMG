# TestSMG
PRUEBA TÉCNICA BACKEND SMG

### Documentación Técnica: Implementación del Template de Servicios en Shopify

Este documento detalla la implementación técnica realizada para la página de servicios en Shopify, de acuerdo con los requerimientos del cliente.

#### **Página de Servicios:**
URL: https://solo-media-group.myshopify.com/pages/servicios
Clave de acceso: ingaug

### Requerimientos Implementados

#### 1. **Crear Productos de Servicios:**
   - **Acción:** Se crearon tres productos de servicios desde la administración de Shopify.
   - **Descripción:** Los productos de servicios fueron definidos en la sección de productos del administrador de Shopify. Estos productos serán utilizados en la página de servicios.

#### 2. **Crear una Colección con los Servicios:**
   - **Acción:** Se creó una colección denominada "Servicios" desde la administración.
   - **Validación:** El template se configuró para que solo muestre los productos asociados a esta colección específica.
   - **Código Relacionado:** En el archivo `product-select.liquid`, se utiliza la colección para listar los productos disponibles.

   
   {% for product in collections['servicios'].products %}
     <option value="{{ product.variants.first.id }}">{{ product.title }} - {{ product.price }}</option>
   {% endfor %}
   

#### 3. **Template para Mostrar los Productos/Servicios:**
   - **Acción:** Se creó un template de página llamado `page.services.liquid`.
   - **Descripción:** Este template muestra todos los productos/servicios en una sola página, incluyendo un botón de "Agregar al Carrito" para cada uno. El producto de regalo no se muestra.
   - **Código Relacionado:** Se incluye la sección `product-select` en el template para manejar la selección de productos y la lógica del carrito.

  
   {%- section 'product-select' -%}
  

#### 4. **Restricción de un Solo Servicio en el Carrito:**
   - **Acción:** Se creó el archivo `product-select.liquid` dentro de la carpeta `sections`.
   - **Descripción:** Este archivo contiene un script que garantiza que solo un servicio puede estar en el carrito a la vez. Si un cliente intenta agregar otro servicio, el carrito se vacía y se reemplaza con el nuevo servicio.
   - **Código Relacionado:** Se implementó la siguiente lógica en el script:

   
   document.getElementById('add-to-cart').addEventListener('click', function() {
     // Código que vacía el carrito y añade el nuevo producto
   });


#### 5. **Schema para Mostrar Reviews:**
   - **Acción:** Se creó una sección llamada `reviews-section.liquid`.
   - **Descripción:** Este schema permite mostrar los reviews generales en la página. No se requiere maquetación o vista avanzada, solo lectura y despliegue de la información.
   - **Código Relacionado:** El template `page.services.liquid` incluye la sección para mostrar los reviews:

   
   {% section 'reviews-section' %}
  

#### 6. **Carga del Producto al Carrito usando AJAX:**
   - **Acción:** Se implementó un script en `product-select.liquid`.
   - **Descripción:** Este script maneja la adición de servicios al carrito usando AJAX, para una experiencia de usuario fluida y sin recargas de página.
   - **Código Relacionado:**

 
   fetch('/cart/add.js', {
     method: 'POST',
     body: formData
   }).then(function(response) {
     // Manejo de la respuesta
   });
 

#### 7. **Producto de Regalo por la Compra de un Servicio:**
   - **Acción:** Se configuró un producto regalo utilizando la aplicación "Gift Box".
   - **Descripción:** Al comprar un servicio, se regala una "Camisa SMG + Lunch Box". Esta configuración se realizó en la administración de Shopify.

#### 8. **Configuración de Envío Gratuito:**
   - **Acción:** Se configuró el envío gratuito desde la administración de Shopify.
   - **Descripción:** Todos los servicios tienen envío gratuito, asegurando que no se cobren costos adicionales por envío.

### Archivos Principales

#### **Archivo:** `page.services.liquid`
   - **Función:** Define la estructura de la página de servicios, incluyendo la selección de productos y la visualización de reviews.
   - **Código:**

   
   {%- comment -%}
     El siguiente JSON define el esquema para Product Select
   {%- endcomment -%}

   <script type="application/json" id="schema-product-select">
   {
     "name": "Product Select",
     "settings": [],
     "presets": [
       {
         "category": "Products"
       }
     ]
   }
   </script>

   {%- assign product_select = settings.schema_product_select | json -%}

   {%- section 'product-select' -%}

   {% section 'reviews-section' %}
  

#### **Archivo:** `product-select.liquid`
   - **Función:** Muestra un selector de productos y maneja la adición al carrito, asegurando que solo un servicio esté en el carrito a la vez.
   - **Código:**

   

   <select id="product-list">
     <option value="">Selecciona un producto</option>
     {% for product in collections['servicios'].products %}
       <option value="{{ product.variants.first.id }}">{{ product.title }} - {{ product.price }}</option>
     {% endfor %}
   </select>

   <button id="add-to-cart">Agregar al Carrito</button>

   <script>
     document.getElementById('add-to-cart').addEventListener('click', function() {
       var productId = document.getElementById('product-list').value;
       if (productId) {
         var formData = new FormData();
         formData.append('id', productId);
         formData.append('quantity', 1);

         // Vaciar el carrito antes de agregar el nuevo servicio
         fetch('/cart/clear.js', {
           method: 'POST'
         }).then(function(response) {
           if (response.ok) {
             // Agregar el nuevo servicio al carrito
             fetch('/cart/add.js', {
               method: 'POST',
               body: formData
             }).then(function(response) {
               if (response.ok) {
                 alert('Servicio agregado al carrito exitosamente.');
                 window.location.href = '/cart'; // Redirigir al carrito después de agregar el servicio
               } else {
                 alert('Hubo un problema al agregar el servicio al carrito. Por favor, inténtalo nuevamente.');
               }
             });
           } else {
             alert('Hubo un problema al limpiar el carrito. Por favor, inténtalo nuevamente.');
           }
         }).catch(function(error) {
           console.error('Error al agregar el servicio al carrito:', error);
           alert('Hubo un error al agregar el servicio al carrito. Por favor, inténtalo nuevamente.');
         });
       } else {
         alert('Selecciona un servicio antes de agregarlo al carrito.');
       }
     });
   </script>
  

Esta documentación abarca la implementación de los requerimientos y los archivos de código asociados, describiendo la funcionalidad clave y el propósito de cada sección.
