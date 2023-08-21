## Enviar mensaje por whatsApp

**Objetivo:** contar con una directiva para agregar la función de enviar mensaje por whatsApp. Se busca encapsular la lógica y dar claridad al botón empleado para la tarea.

### Antecedentes

Manera directa de enviar mensajes:

```HTML
<a href="https://wa.me/+15550123?text=Hola,%20Mundo" target="_blank">Enviar mensaje</a>
```

### Propuesta de directiva

```TS
import { Directive, Input, HostListener } from '@angular/core';

@Directive({
  selector: '[sendByWA]'
})
export class SendByWADirective {
  private waUrl = 'https://wa.me/';
  private link: any;
  @Input() phoneNumber?: string;
  @Input() message = '';

  constructor() {
    this.link = document.createElement('a');
    this.link.target = '_blank';
  }

  @HostListener('click') onClick() {
    if (!this.phoneNumber) return;
    console.log('works...')
    const encodedMessage = encodeURIComponent(this.message);
    const waUrl = `${this.waUrl}${this.phoneNumber}?text=${encodedMessage}`;
    this.link.href = waUrl;
    this.link.click();
  }
}
```

## Implementación

```TS app.component.html
<button sendByWA phoneNumber="+15550123" message="Hola, mundo">Enviar whatsApp</button>
