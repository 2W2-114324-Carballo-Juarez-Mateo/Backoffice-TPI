# RULES — Sin store global entre apps

1. **No** existe un store global compartido entre apps distintas (NgRx es **por app**). No diseñes un "store central" que todas las apps lean.
2. **Servidor = fuente de verdad:** el estado de negocio vive en el backend; cada app lo consulta por su BFF.
3. **Custom Events** (`auth:logout`, `auth:session-expired`, `data:changed`) son **notificaciones**, no transporte de estado. No pongas datos de negocio importantes como payload de un evento.
4. **Storage** (localStorage/sessionStorage) solo para **preferencias y caché ligera no sensibles**.
5. Si dos apps necesitan el mismo dato, lo leen del servidor (no lo dupliquen en memoria entre sí).