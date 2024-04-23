**Objetivo:** En esta ocasión vamos a explicar una de las maneras más sencillas de crear una máquina tragamonedas para Angular

## Beneficios
- Disponible para todos los módulos de la aplicación
- Créditos configurables
- Costo por juego personalizado
- Listado de emojis personalizable

## Antes de empezar necesitamos

- Proyecto existente versión Angular 17 o superior
- Node Js compatible

## Conocimientos recomendados

- Angular standalone components
- Angular signals
- HTML y CSS

## Definimos el componente

```javascript
import { CommonModule } from '@angular/common';
import { Component, OnInit, input, model, signal } from '@angular/core';
import { winningCombinations } from '../../consts/winningCombinations';

@Component({
  standalone: true,
  imports: [CommonModule],
  selector: 'app-slot-machine',
  templateUrl: './slot-machine.component.html',
  styleUrl: './slot-machine.component.css'
})
export class SlotMachineComponent implements OnInit {}
```

## Agregamos propiedades del componente

- `reels` contendrá la matriz de combinaciones
- `creditsRequired` número que representa los creditos necesarios para jugar
- `winCredits` propiedad de entrada que representa la cantidad de créditos ganados
- `items` propiedad de entrada que es requerida para rellenar `reels`
- `credits` propiedad de 2 vías para gestionar créditos del jugador
- `timeout` número que representa tiempo de giro de la máquina

```javascript
export class SlotMachineComponent implements OnInit {
  reels = signal([['']]);
  creditsRequired = input(1);
  winCredits = input(10);

  items = input.required<string[]>();
  credits = model(1);
  private timeout = 3000;
}
```

## Agregamos método privado para cargar matriz
**Algoritmo:**
1. Crear const `newReels` con arreglo vacío
2. Iterar desde 0 hasta 3 de 1 en 1
   - Crear const `randomEmojis` con arreglo vacío
   - Iterar desde 0 hasta 3 de 1 en 1
     - Obtener índice aleatorio
     - Obtener elemento aleatorio
     - Agregar elemento a `randomEmojis`
   - Agregar fila a `newReels`
3. Asignar `newReels` a `reels`

``` JavaScript
export class SlotMachineComponent {
//...
  private loadReels(): void {
    const newReels = [];
    for (let i = 0; i < 3; i++) {
      const reelEmojis = [];
      for (let j = 0; j < 3; j++) {
        const randomIndex = Math.floor(Math.random() * this.items().length);
        const randomEmoji = this.items()[randomIndex];
        reelEmojis.push(randomEmoji);
      }
      newReels.push(reelEmojis);
    }
    this.reels.set(newReels)
  }
//...
}
```
## Agregamos método privado para validar combinaciones ganadoras

Archivo con combinaciones

``` JavaScript
export const winningCombinations = [
  [[0, 0], [0, 1], [0, 2]], // Primera fila
  [[1, 0], [1, 1], [1, 2]], // Segunda fila
  [[2, 0], [2, 1], [2, 2]], // Tercera fila
  [[0, 0], [1, 0], [2, 0]], // Primera columna
  [[0, 1], [1, 1], [2, 1]], // Segunda columna
  [[0, 2], [1, 2], [2, 2]], // Tercera columna
  [[0, 0], [1, 1], [2, 2]], // Diagonal descendente
  [[2, 0], [1, 1], [0, 2]], // Diagonal ascendente
];
```
**Algoritmo:**
1. Obtener `winningCombinations`
2. Iterar `winningCombinations`
    - Asignar constante `emoji` desde las combinaciones
    - Validar si el emoji aparece en todas las combinaciones de la fila
3. Retornar resultado

``` JavaScript
private validateReels(): string | null {
    const combinations = winningCombinations;
    for (const combination of combinations) {
      const emoji = this.reels()[combination[0][0]][combination[0][1]];
      if (
        combination.every(([row, col]) => this.reels()[row][col] === emoji)
      ) {
        return emoji;
      }
    }
    return null;
  }
```

## Agregamos método público para jugar

**Algoritmo:**
1. Validar si se cuenta con los créditos requeridos
2. Descontar créditos según los créditos requeridos
3. Repetir `loadReels` cada 100ms
4. Asignar cuenta atrás para detener repetición
5. Validar combinaciones ganadoras para asignar créditos ganados

``` JavaScript
spin(): void {
    if (this.credits() < this.creditsRequired()) return;
    this.credits.update(value => value - this.creditsRequired());
    const interval = setInterval( () => {
      this.loadReels()
    }, 100);
    setTimeout(() => {
      clearInterval(interval);
      if (this.validateReels()) {
        alert('ganador');
        this.credits.update(value => value + this.winCredits());
      }
      }, this.timeout);
  }
```

## Agregamos método para insctrucciones iniciales

Sólo queda agregar `ngOnInit` para llamar `loadReels()`. El archivo TS completo quedaría así:

``` JavaScript
import { CommonModule } from '@angular/common';
import { Component, OnInit, input, model, signal } from '@angular/core';
import { winningCombinations } from '../../consts/winningCombinations';

@Component({
  standalone: true,
  imports: [CommonModule],
  selector: 'app-slot-machine',
  templateUrl: './slot-machine.component.html',
  styleUrl: './slot-machine.component.css'
})
export class SlotMachineComponent implements OnInit {
  reels = signal([['']]);
  creditsRequired = input(1);
  winCredits = input(10);

  items = input.required<string[]>();
  credits = model(1);
  private timeout = 3000;
  
  ngOnInit(): void {
    this.loadReels();
  }

  spin(): void {
    if (this.credits() < this.creditsRequired()) return;
    this.credits.update(value => value - this.creditsRequired());
    const interval = setInterval( () => {
      this.loadReels()
    }, 100);
    setTimeout(() => {
      clearInterval(interval);
      if (this.validateReels()) {
        alert('ganador');
        this.credits.update(value => value + this.winCredits());
      }
      }, this.timeout);
  }
  
  private loadReels(): void {
    const newReels = [];
    for (let i = 0; i < 3; i++) {
      const reelEmojis = [];
      for (let j = 0; j < 3; j++) {
        const randomIndex = Math.floor(Math.random() * this.items().length);
        const randomEmoji = this.items()[randomIndex];
        reelEmojis.push(randomEmoji);
      }
      newReels.push(reelEmojis);
    }
    this.reels.set(newReels)
  }

  private validateReels(): string | null {
    const combinations = winningCombinations;
    for (const combination of combinations) {
      const emoji = this.reels()[combination[0][0]][combination[0][1]];
      if (
        combination.every(([row, col]) => this.reels()[row][col] === emoji)
      ) {
        return emoji;
      }
    }
    return null;
  }
}
```

## Archivos restantes
Plantilla básica
``` HTML
<div id="slotMachine">
  @for (reel of reels(); track $index) {
    @for (item of reel; track $index) {
      {{item}}
    }
  }
  <button (click)="spin()">JUGAR</button>
</div>
```
Estilos
``` CSS
#slotMachine {
  display: flex;
  flex-wrap: wrap;
  font-size: 96px;
  width: 417px;
  border: 4px solid #000;
  border-radius: 40px;
  padding: 16px;
  margin: 32px;
  background: #222;
}
button {
  background: linear-gradient(25deg,blue, red);
  border-radius: 8px;
  border: none;
  color:#ddd;
  cursor: pointer;
  font-size: 16px;
  margin: auto;
  padding: 16px;
  transform: translateY(16px);
  transition: all .3s;
  width: 33%;
  &:active {
    transform: translateY(16px) scale(.7);
  }
}
```

## En conclusión

Ésta es la versión más reciente de una máquina tragamonedas que he desarrollado y compartido. Lo considero una gran práctica para aprender `signal`.

## Extra
- Te reto a agregar premios distintos según las combinaciones
