Tito es un voluntario que se ofreció para participar de un estudio en el que se plantea medir cómo afecta 
el consumo de algunas sustancias al rendimiento de los deportistas.

Tito pesa actualmente 70 kilos. Tiene la gran ventaja de que cada bebida que toma _reemplaza_ a la anterior. Hoy modelaremos tres bebidas:

* **Whisky**: el whisky provoca sueño, mareo y jaquecas. Un deportista con whisky en su sistema tiene un rendimiento de 0.9 elevado a la dosis consumida, en centímetros cúbicos.
* **Tereré**: el tereré es diurético, laxante y estimulante del rendimiento. Un deportista con tereré en su sistema tiene un rendimiento de 0.1 por cada centímetro cúbico de dosis. Sin embargo, nunca rinde menos que 1.
* **Cianuro**: el cianuro no favorece al rendimiento, sino que el deportista, luego de consumirlo queda como muerto, llenos de abulia y de marasmo. Con cianuro, el rendimiento es siempre 0.

> 1. Hacer que Tito tome cierta cantidad de una bebida, con el mensaje `consumir(cantidad, bebida)`
> 2. Conocer la velocidad de Tito, mediante el mensaje `velocidad()`.

La velocidad de Tito (en m/s) se calcula como el rendimiento de la sustancia por la inercia base de Tito (490 kg*m/s) dividido su peso.

**¡No vale guardarse la velocidad!**


## Pista
Para calcular la potencia los número entienden el mensaje `**`. Por ejemplo, '2 al cubo' se expresa `2 ** 3`

Además entienden los mensajes `min` y `max` que permiten hacer:

* `1.max(3)`  Retorna 3, por ser el máximo
* `1.min(3)`  Retorna 1, por ser el mínimo


## Conclusión

En este ejercicio el _polimorfismo_ aparece como `tito` utiliza las distintas sustancias para conocer su rendimiento.

Cabe destacar que para lograrlo, las sustancias deben entender el mismo mensaje, incluyendo nombre **y parámetros**. Esto significa que si para conocer el rendimiento que algunas sustancias es necesario pasar algún parámetro en el mensaje (como la cantidad de la dosis para el `whisky` y `terere`) entonces todas las sustancias (incluyendo el `cianuro`) lo deberían esperar.


También hay que diseñar qué datos son _atributos_ y cuáles _métodos de consulta_ de `tito`. El enunciado pide que `velocidad` **no** sea un atributo ya que se obtiene de un cálculo que involucra el rendimiento, inercia y peso de `tito`.


# parcial 2 de objetos

object indio {
  var energia = 100
  var estaInspirado = true
  const gira = []
  // PARTE 1 > <

  method energia() = energia

  method estaInspirado() = estaInspirado

  method agregarRecital(unRecital) {
    gira.add(unRecital)
  }
  method suspenderRecital(unRecital) {
    gira.remove(unRecital)
  }

  method descansar(horas) {
    energia = energia + horas * 10
    estaInspirado = true
  }
  method estaAgotado() {
    return energia <= 0
  }

  method salirAlEscenario() {
    if (self.estaInspirado() and not self.estaAgotado()) {
      energia -= 10
    }
    energia -= 10
    estaInspirado = false
  }

  // PARTE 2 > <

  method recitalesEnCapital() {
    return gira.filter({g => g.estaEnCapital()})
  }
  method recitalesEnProvincia() {
    return gira.filter({g => !g.estaEnCapital()})
  }
  method nombresDeLasLocaciones() {
    return gira.map({g => g.localizacion().nombre()})
  }
  method vender(entradas) {
    gira.forEach({g => g.venderEntradas(entradas)})
  }
  method giraEsAgotada() {
    return gira.all({g => g.esMasivo()})
  }
  method totalEntradasVendidasCapital() { // HACER
    return gira.sum({g => g.filter({g => g.estaEnCapital()}).cantEntradasVendidas()}) // hacer
  }
  method elRecitalMasGrandeDeAlgunaProvincia() {
    return self.recitalesEnProvincia().max({g => g.capacidad()})
  }
  method provinciaConElRecitalMasGrande() {
    return self.elRecitalMasGrandeDeAlgunaProvincia().provincia()
  }
  method hayRecitalCostoso() {
    return gira.any({g => g.calcularCosto() > 5000000})
  }
}

// PARTE 3 > <
class Recital {
  const localizacion
  var cantEntradasVendidas = 0
  method cantEntradasVendidas() = cantEntradasVendidas
  // De todos los recitales se debe poder consultar:
  method provincia() // method provincia() = "jujuy"
  method estaEnCapital() {
    return self.provincia() == "CABA"
  }
  method capacidadTotal() = localizacion.capacidadMaxima()
  method esMasivo() {
    return self.capacidadTotal() > 40000
  }
  method esEpico() {
    return cantEntradasVendidas - self.capacidadTotal() <= self.capacidadTotal() * 0.05 // igual o menor 5% ?
  }
  // Y todo recital debe poder:
  method venderEntradas(cantidad) {
    cantEntradasVendidas += cantidad
  }
  method costoBase() = localizacion.capacidadMaxima() * 20000
  method recargoPorEventoMasivo() {
    if (self.esMasivo()) {
      return 100000
    } else {
      return 0
    }
  }
  method costoEspecificoPorUbicacion() { // no se si lo tengo que modelar yo
  var auxiliar = 4000
    if (self.estaEnCapital()) {
      auxiliar  = 70000
    }
    return auxiliar
  }
  method calcularCosto() {
    return self.costoBase() + self.recargoPorEventoMasivo() + self.costoEspecificoPorUbicacion()
  }
  method localizacion() = localizacion
}
// PARTE 4 > <
class RecitalCapital inherits Recital {
  method getProvincia() = "CABA"
  var costoSeguridadExtra = 500000
  method costoSeguridadExtra() = costoSeguridadExtra
  method costoEspecifico() = costoSeguridadExtra
  override method venderEntradas(cantidad) {
    super(cantidad)
    costoSeguridadExtra += 100 * cantidad
  }
}
class RecitalProvincia inherits Recital {
  const provincia
  const kmDistanciaCaba = 400
  const gastoTranslado = 1000000
  method getProvincia() = provincia
  method costoEspecifico() = gastoTranslado
  method esEpico(cantidad) {
    return localizacion.habilitado() and kmDistanciaCaba > 700
  }
}

// PARTE 5 > <
class Localizacion {
  const nombre
  method nombre() = nombre
  method capacidadMaxima()
  method habilitado()
}

class Teatro inherits Localizacion {
  const asientos
  const salidasDeEmergencias
  method asientos() = asientos
  method salidasDeEmergencias() = salidasDeEmergencias
  override method capacidadMaxima() {
    return 0.max(self.asientos() - self.salidasDeEmergencias()) * 50
  }
  override method habilitado() {
    return salidasDeEmergencias >= 2
  }
  
}

class Estadio inherits Localizacion {
  const metrosCuadrados
  const cantSectoresEnGradas
  method metrosCuadrados() = metrosCuadrados
  method cantSectoresEnGradas() = cantSectoresEnGradas
  override method capacidadMaxima() {
    return self.metrosCuadrados() * 4 + cantSectoresEnGradas * 5000
  }
  override method habilitado() {
    return (self.cantSectoresEnGradas() > 0) and (self.metrosCuadrados() > 100)
  }
  
}

// mas o menos el parcial empezo 8:40

// 40 !g.estaEnCapital()
// 43 g.localizacion().nombre()
// 57 No había que incluir CABA
// 75 borrar esProvincia()
// 123 get
// 125 Falta inicializar en 1.000.000
// 146 faltó *50
// 149 sin definir

// 51 totalEntradasVendidasCapital // no se si lo hice bien

// 90 variable local innecesaria
// 98 Se modelaba en cada clase hija y lo hiciste, pero como costoEspecífico
// 113 /no era un atributo, falta el método
// 117 super() no funcionaba
// 127 super() no sabia








import hola.*

describe "Escenario inicial (Fixture)" {

  //Falta "const =", no empiezan en mayusculas, y falta nombre de las locaciones, 
  const granRex = new Teatro(asientos = 3300, salidasDeEmergencias = 6)
  const obrasSanitarias = new Estadio(metrosCuadrados = 500, cantSectoresEnGradas = 2)
  const estadioUnico = new Estadio(metrosCuadrados = 7000, cantSectoresEnGradas = 10)

  //Falta "const ="
  //Mal escritos las locaciones al instanciar por ende no se pueden utilizar aquí
  const capital1 = new RecitalCapital(localizacion = granRex)
  const capital2 = new RecitalCapital(localizacion = obrasSanitarias)
  const provincia = new RecitalProvincia(localizacion = estadioUnico, provincia = "BuenosAires", gastoTranslado = 2000000)

  //Falta Initialize()
  indio.agregarRecital(capital1)
  indio.agregarRecital(provincia)


  test "test 1" {
    assert.equals(3000, granRex.capacidadMaxima())
  }
  test "test 2" {
    assert.equals(true, estadioUnico.habilitado())
    assert.equals(78000, estadioUnico.capacidadMaxima())
  }
  test "test 3" {
    capital1.venderEntradas(10)
    assert.equals(10, capital1.cantEntradasVendidas())
    assert.equals(501000, capital1.costoSeguridadExtra())
  }
  test "test 4" {
    assert.equals(3000000, provincia.calcularCosto())
  }

  test "test 5" {
    indio.salirAlEscenario()
    indio.salirAlEscenario()
    assert.equals(70, indio.energia())
  }
  test "test 6" {
    indio.salirAlEscenario()
    indio.descansar(2)
    assert.equals(110, indio.energia())
    assert.equals(true, indio.estaInspirado())
  }
  test "test 7" {
    capital1.venderEntradas(100)
    indio.agregarRecital(capital2)
    capital2.venderEntradas(250)
    assert.equals(350, indio.totalEntradasVendidasCapital())
  }
  test "test 8" {
    assert.equals(false, indio.giraEsAgotada())
  }
  test "test 9" {
    provincia2 = new RecitalProvincia(localizacion = Cervantes, provincia = "Salta")
    assert.equals("BuenosAires", indio.provinciaConElRecitalMasGrande())
  }

}

// 1,56000 algo
