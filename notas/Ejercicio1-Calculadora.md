# Ejercicio 1 - CalculadoraPrecios (test rojo -> verde)

## Contexto

Vamos a construir una clase de negocio que aplica descuentos.

```
CalculadoraPrecios.Calcular(precio, porcentajeDescuento) -> decimal
CalculadoraPrecios.CalcularTotal(IEnumerable<decimal> precios, decimal descuento) -> decimal
```

**Reglas del negocio:**
- El descuento va de 0 a 100
- Si el descuento es menor que 0 o mayor que 100, lanza `ArgumentOutOfRangeException`
- Si la lista de precios es nula o vacia, lanza `ArgumentException`
- El calculo se redondea a 2 decimales

---

## PASO 1 - Crear el proyecto

```powershell
dotnet new classlib -o Inventario.Core
dotnet sln Inventario.slnx add Inventario.Core\Inventario.Core.csproj
dotnet add Inventario.Tests\Inventario.Tests.csproj reference Inventario.Core\Inventario.Core.csproj
```

---

## PASO 2 - El test (se escribe ANTES del codigo)

Abrir `Inventario.Tests\UnitTest1.cs`, **borrar todo**, pegar:

```csharp
using Inventario.Core;

namespace Inventario.Tests;

public class CalculadoraPreciosTests
{
    [Theory]
    [InlineData(100, 0, 100)]      // sin descuento
    [InlineData(100, 10, 90)]      // 10% de descuento
    [InlineData(100, 50, 50)]      // 50% de descuento
    [InlineData(200, 25, 150)]     // sobre otro precio
    public void CalcularPrecio_AplicarDescuento_RetornaPrecioFinal(
        decimal precio, decimal descuento, decimal esperado)
    {
        var resultado = CalculadoraPrecios.Calcular(precio, descuento);

        Assert.Equal(esperado, resultado);
    }

    [Fact]
    public void CalcularPrecio_ConDescuentoNegativo_LanzaExcepcion()
    {
        Assert.Throws<ArgumentOutOfRangeException>(
            () => CalculadoraPrecios.Calcular(100, -5));
    }

    [Fact]
    public void CalcularPrecio_ConDescuentoMayorACien_LanzaExcepcion()
    {
        Assert.Throws<ArgumentOutOfRangeException>(
            () => CalculadoraPrecios.Calcular(100, 150));
    }
}
```

---

## PASO 3 - Verificar que FALLA

```powershell
dotnet test
```

**Debe mostrar un error** tipo:
```
error CS0103: El nombre 'CalculadoraPrecios' no existe en el contexto actual
```

**Ese error es el objetivo.** Si pasa a la primera, no estas probando nada.

---

## PASO 4 - Prompt para el agente

> "En `Inventario.Core/CalculadoraPrecios.cs` implementa la clase
> `CalculadoraPrecios` con metodo estatico `Calcular(decimal precio, decimal
> descuento)` que devuelva el precio con el descuento aplicado, redondeado a 2
> decimales. Si el descuento es menor que 0 o mayor que 100, lanza
> `ArgumentOutOfRangeException`. NO anadas nada mas. Despues ejecuta
> `dotnet test` y pega la salida real."

---

## PASO 5 - Verificar que PASA

```powershell
dotnet test
```

**Debe decir:** `total: 7, correcto: 7, con errores: 0`

(4 casos del Theory + 2 Fact + 1 test de ejemplo que sigue en la plantilla)

---

## PASO 6 - Commit

```powershell
git add .
git commit -m "feat: calculadora de precios con tests"
```

---

## Notas de aprendizaje

| Concepto | Que es |
|---|---|
| `[Fact]` | Un caso de prueba |
| `[Theory]` | Un caso con varios valores (`[InlineData]`) |
| `Assert.Equal` | Compara valores |
| `Assert.Throws<T>` | Verifica que se lance una excepcion concreta |
| Test rojo | Test que falla antes de implementar |

**Los nombres de los tests se leen como frases:**
"CalcularPrecio, aplicando descuento, retorna precio final"

Eso es mucho mas util que `Test1()` cuando algo falla y tienes 40 tests.