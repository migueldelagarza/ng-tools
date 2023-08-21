## Servicio Angular para copiar texto a portapapeles

*copyToClipBoard.service.ts*
```TS
import { Injectable, inject } from "@angular/core";
import { Clipboard } from '@angular/cdk/clipboard';
import { MatSnackBar } from '@angular/material/snack-bar'; // Librería opcional para alertas

@Injectable({ providedIn: 'root' })
export class ClipboardService {
  private clipboard = inject(Clipboard);
  private snack = inject(MatSnackBar)

  copyToClipboard(text: string): boolean {
    if (!text || text == null || text == undefined) return false;
    if (!this.clipboard.copy(text)) return false;
    this.snack.open('¡Comando copiado al portapapeles!', '', { duration: 2000 });
    return true;
  }
}

```
