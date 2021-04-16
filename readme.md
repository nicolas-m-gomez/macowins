---


---

<h1 id="macowins"><strong>Macowins</strong></h1>
<h2 id="precio-de-venta-de-una-prenda">Precio de venta de una prenda</h2>
<p>El cálculo del precio estará determinado por el estado de la prenda. Cada prenda tiene un precio base a partír del cual se calcula el precio unitario.<br>
El tipo de prenda se identifica con el enum TipoPrenda.</p>
<pre><code>class Prenda{
  BigDecimal precioBase
  String Descripcion
  TipoPrenda tipoPrenda
  EstadoPrenda estadoPrenda
...
  BigDecimal precioUnitario(){
    return estadoPrenda.precioModificado(this.precioBase)
  }
}
interface EstadoPrenda{
  BigDecimal precioModificado(BigDecimal precioBase)
}
class Nueva extends EstadoPrenda{
...
  BigDecimal precioModificado(precioBase){
    return precioBase
  }  
}
class Promocion{
  BigDecimal montoDescuento
...
  BigDecimal precioModificado(precioBase){
    return precioBase - montoDescuento
  }    
}
class Liquidacion{
  final BigDecimal porcentajeDescuento = 50
...
  BigDecimal precioModificado(precioBase){
    return precioBase * (1 - porcentajeDescuento/100)
  }
}
</code></pre>
<h2 id="ganancias-de-un-día">Ganancias de un día</h2>
<p>Primero, el precio final de venta se calcula a partir de la función <em>precioVentaNeto()</em> que suma el precio unitario de las prendas multiplicado por la cantidad. El precio de venta final se calcula a partir de la forma de pago.</p>
<pre><code>class Venta{
  ...
  BigDecimal precioVentaNeto(){
    return this.itemsVenta.map(item -&gt; item.precioItemVenta()).sum()
  }
  BigDecimal precioVentaFinal(){
    return this.formaDePago.recargo(this.precioVentaNeto())
  }
}
interface FormaDePago{
  BigDecimal recargo(BigDecimal precioNeto)
}
class Efectivo extends FormaDePago{
  BigDecimal recargo(BigDecimal precioNeto){
    return precioNeto
  }
}
class TarjetaCredito extends FormaDePago{
  ... 
  BigDecimal recargo(BigDecimal precioNeto){
    return precioNeto + cantidadCuotas * COEFICIENTE_RECARGO + 0.01 * precioNeto
  }
}
</code></pre>
<p>Por último, para obtener el listado de ganancias del día se debe usar la función <em>gananciasDel(unaFecha)</em> de la clase Ganancias que filtra las ventas del día que se le envíe como parámetro.</p>
<pre><code>class Ganancias{
  ...
  BigDecimal gananciasDel(LocalDateTime fecha){
    return this.ventas.filter(venta -&gt; venta.getFechaVenta().equals(fecha)).map(venta -&gt; venta.precioVentaFinal()).sum()
  }
}
</code></pre>
<h2 id="diagrama-de-clases">Diagrama de clases</h2>
<p><img src="https://raw.githubusercontent.com/nicolas-m-gomez/macowins/master/Diagrama%20de%20clases.png" alt="Diagrama de clases"></p>

