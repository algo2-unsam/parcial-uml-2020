
## Parcialito: Comunicación de un diseño

El trabajo es de caracter individual.
El vencimiento será el domingo 05/05 a las 12:00 del mediodía.

### Enunciado

Dada la siguiente solución se pide que 

- comunique el diagrama de clases 
- comunique el diagrama de secuencia para el caso de uso “Ganó Apuesta”, donde
  - un jugador que apostó pleno y ganó (salió el 25)
  - un jugador que hizo apuesta múltiple y no ganó (invente ud. el ejemplo)
- indique qué patrones de diseño reconoce, justifique su decisión.

Para ello recomendamos la lectura del material [Guías para comunicar un diseño](https://docs.google.com/document/d/1HGdGdDG7RAhL5j45UOFGK3F5sV2-rKHVHmPoYawHS5Y/edit?usp=sharing).

Los diagramas los pueden hacer con [Draw](draw.io) en google drive, y luego bajarlos localmente, o bien pueden hacerlos a mano alzada y escanearlos o sacarles una foto. El archivo lo suben a este repo y luego lo pushean.

Como paso adicional deben ingresar al [Campus Virtual ECyT](http://campusvirtualecyt.unsam.edu.ar/) y subir la misma solución a la tarea "Parcial comunicación del Diseño".


### Solución

```xtend
class Jugador {
  int montoGanado (lo que ganó hasta ahora)
  TipoJugador tipoJugador

  def void procesarApuesta(Apuesta apuesta) {
      if (apuesta.ganadora()) {
        montoGanado = montoGanado + apuesta.montoRecompensa()
      }
  }

  def int montoPorApuesta() {
    return tipoJugador.montoPorApuesta(this)
  }
}

class Apuesta {
  int numeroGanador
  int montoApostado

  def boolean ganadora()

  def int montoRecompensa() { 
    return montoApostado * this.efectoMultiplicador()) 
  }

  abstract def int efectoMultiplicador()
}

class ApuestaPleno extends Apuesta {
  int numeroApostado
  override ganadora() { return numeroApostado === numeroGanador }
  override efectoMultiplicador() {
    return if (montoApostado > 10000) 3 else 2
  }
}

class ApuestaPar extends Apuesta {
  override ganadora() { return numeroGanador.even() }
  override efectoMultiplicador() { return 1 }
}

class ApuestaMultiple extends Apuesta {
  Set<Apuesta> apuestas 


  override ganadora() {
  return apuestas.all [ apuesta | apuesta.ganadora()
  }

  override efectoMultiplicador() {
    return apuestas.fold(0, [ acum, apuesta | 
                              acum + apuesta.efectoMultiplicador() ])
  }
}

interface TipoJugador {
  def int montoPorApuesta(Jugador jugador)
}

class Gastador implements TipoJugador {
  override montoPorApuesta(Jugador jugador) {
    return jugador.montoGanado()
  }
}

class Conservador implements TipoJugador {
  override montoPorApuesta(Jugador jugador) {
    return 50
  }
}
```
