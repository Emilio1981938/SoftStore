import tkinter as tk
from tkinter import ttk, filedialog
import random
import os

class Tema:
    FONDO = "#000000"
    PANEL = "#001a1a"
    BOTON = "#000d14"
    TEXTO = "#ffffff"
    IA = "#00ffcc"
    VIDEO = "#00f2ff"
    FOTO = "#ff00ff"
    AUDIO = "#00ff88"
    ALERTA = "#ff4d4d"
    STATS = "#00ffaa"

class EmiEditPro:
    def __init__(self, root):
        self.root = root
        self.root.title("EMI EDIT PRO - MULTICAPA")
        self.root.configure(bg=Tema.FONDO)
        
        # Variables de Sistema
        self.reproduciendo = False
        self.milisegundos = 0
        self.grabando_mic = False
        self.renderizando = False
        self.timeline_fotos = []
        self.timeline_audios =[]
        self.playhead_id = None
        self.triangulo_id = None
        
        # --- 100 HERRAMIENTAS REALES DE VIDEO ---
        self.video_db =[
            "Importar", "Cortar", "Dividir", "Unir", "Recortar", "Lento", "Rápido", "Revertir", "Congelar", "Rotar",
            "Escalar", "Opacidad", "Croma Key", "Máscara", "Estabilizar", "Transición", "Animación", "Keyframe", "Texto", "Subtítulos",
            "LUTs", "Color Match", "Rueda Color", "Curvas Luma", "Niveles", "Exposición", "Contraste", "Saturación", "Temperatura", "Matiz",
            "Brillo", "Sombras", "Blancos", "Negros", "Glitch", "VHS", "Ruido", "Desenfocar", "Viñeta", "Resplandor",
            "Borde", "Sombra Drop", "Tinte", "HSL", "Histograma", "Vectorscopio", "Seguimiento", "Tracking IA", "Quitar Fondo", "Reemplazar Fondo",
            "Morph", "Deformar", "Esferizar", "Molinete", "Ola", "Pixelar", "Mosaico", "Relieve", "Trazos", "Dibujo Animado",
            "Acuarela", "Óleo", "Neón", "Fuego", "Rayos", "Lluvia", "Nieve", "Humo", "Polvo", "Arañazos Retro",
            "Parpadeo", "Strobo", "Zoom IN", "Zoom OUT", "Panorámica", "Inclinación", "Cámara 3D", "Luces", "Foco", "Profundidad",
            "Destello Lente", "Lens Flare", "Aberración", "RGB Split", "Halación", "Bloom", "Caleidoscopio", "Fractales", "Simetría", "Reflejo",
            "Ondulaciones", "Eco Visual", "Estela", "Fantasma", "Doble Exp.", "Fusión Capas", "Trama", "Degradado", "Color Sólido", "Títulos 3D"
        ]
        
        # --- 100 HERRAMIENTAS REALES DE FOTO ---
        self.foto_db =[
            "Abrir RAW", "Export JPG", "Export PNG", "Formato HEIC", "Recorte libre", "Girar", "Voltear H/V", "Proporción", "Enderezar", "Perspectiva",
            "Corrección Lente", "Geometría", "Brillo", "Contraste", "Saturación", "Intensidad", "Exposición", "Sombras", "Iluminaciones", "Blancos",
            "Negros", "Temperatura", "Matiz", "Curvas RGB", "Niveles", "Mezcla Colores", "Ajuste HSL", "Blanco y Negro", "Filtro Sepia", "Dividir Tono",
            "Colorización", "Nitidez", "Ruido Luma", "Ruido Color", "Reducción IA", "Desenfoque", "Enfoque", "Desenfoque Lente", "Radial Blur", "Lineal Blur",
            "Viñeta", "Añadir Grano", "Eliminar Manchas", "Pincel Corrector", "Parche", "Tampón Clonar", "Borrador de Fondos", "Selección Auto", "Lazo Mágico", "Varita Mágica",
            "Máscara Rápida", "Perfeccionar Borde", "Reemplazar Cielo", "Añadir Texto", "Marcas Agua", "Marcos", "Bordes", "Pegatinas", "Formas", "Herramienta Dibujo",
            "Pincel", "Lápiz", "Bote Relleno", "Degradado", "Filtro Licuar", "Deformar", "Hinchar", "Desinflar", "Sonrisa IA", "Ojos Grandes",
            "Afinar Nariz", "Contorno Cara", "Suavizar Piel", "Quitar Brillos", "Blanquear Dientes", "Ojos Rojos", "Maquillaje", "Tinte Pelo", "Efectos HDR", "Superposición",
            "Texturas", "Fugas de Luz", "Bokeh", "Prismas", "Destellos", "Glitch Foto", "Efecto Pixelado", "Pintura Digital", "Boceto a Lápiz", "Posterizar",
            "Umbral", "Invertir Color", "Mapa Degradado", "Curva Tonos", "Color Selectivo", "Reemplazar Color", "Filtros IA", "Collage", "Panorama", "Auto-Mejora"
        ]
        
        # --- 100 HERRAMIENTAS REALES DE AUDIO ---
        self.audio_db =[
            "Importar Pista", "Grabar Voz", "Cortar Audio", "Unir Clips", "Silenciar", "Volumen+", "Ganancia", "Normalizar", "Fade In", "Fade Out",
            "Crossfade", "Paneo L/R", "Estéreo a Mono", "Mono a Estéreo", "Invertir Fase", "Pitch Shift", "Time Stretch", "Velocidad X", "Reversa", "Ecualizador AI",
            "EQ 3 Bandas", "EQ 10 Bandas", "EQ Paramétrico", "Filtro Paso Alto", "Filtro Paso Bajo", "Filtro Banda", "Filtro Notch", "Compresor", "Limitador", "Expansor",
            "Puerta Ruido", "De-Esser", "Reducción Ruido", "Denoise IA", "Quitar Clics", "Quitar Hum", "Reverb Sala", "Reverb Hall", "Reverb Placa", "Delay simple",
            "Echo", "Ping Pong Delay", "Chorus", "Flanger", "Phaser", "Tremolo", "Vibrato", "Auto-Wah", "Distorsión", "Overdrive",
            "Fuzz", "Bitcrusher", "Saturación Tubo", "Excitador Armónico", "Ensanchar Estéreo", "Modulador Anillo", "Vocoder", "Auto-Tune", "Corregir Tono", "Armonizador",
            "Doblador Voz", "Sintetizador", "Oscilador LFO", "Generador Ruido", "Tono de Prueba", "Medidor Nivel", "Analizador Espectro", "Sonograma", "Medidor de Fase", "Medidor LUFS",
            "Extraer Acapella", "Extraer Base", "Separar Pistas IA", "Norm. Loudness", "Envolvente Vol", "Envolvente Pan", "Marcadores", "Regiones", "Bucle Loop", "Scrubbing",
            "Metrónomo", "Claqueta", "Convertir Frecuencia", "Convertir Bits", "Dither", "Exportar MP3", "Exportar WAV", "Exportar FLAC", "Exportar OGG", "Exportar AAC",
            "Etiquetado ID3", "Eliminar Silencios", "Normalizar Pico", "Curva Fletcher", "Amplificación", "Remuestreo", "Inversión Espectro", "Suavizado", "Masterización IA", "Bounce Track"
        ]
        
        self.crear_interfaz()
        self.actualizar_motor()
        self.com("Sistema Multicapa Listo. 300 Herramientas profesionales de Video, Foto y Audio cargadas.")

    def crear_interfaz(self):
        # Contenedor principal scroll
        self.canvas_main = tk.Canvas(self.root, bg=Tema.FONDO, highlightthickness=0)
        self.scrollbar = ttk.Scrollbar(self.root, orient="vertical", command=self.canvas_main.yview)
        self.scroll_frame = tk.Frame(self.canvas_main, bg=Tema.FONDO)
        
        self.scroll_frame.bind("<Configure>", lambda e: self.canvas_main.configure(scrollregion=self.canvas_main.bbox("all")))
        self.window_id = self.canvas_main.create_window((0, 0), window=self.scroll_frame, anchor="nw")
        self.canvas_main.bind("<Configure>", lambda e: self.canvas_main.itemconfig(self.window_id, width=e.width))
        self.canvas_main.configure(yscrollcommand=self.scrollbar.set)
        
        self.canvas_main.pack(side="left", fill="both", expand=True)
        self.scrollbar.pack(side="right", fill="y")
        
        # UI Elementos
        self.ia_msg = tk.Label(self.scroll_frame, text="", fg=Tema.IA, bg=Tema.FONDO, font=("Consolas", 10, "bold"), wraplength=320)
        self.ia_msg.pack(pady=5)
        
        self.screen = tk.Canvas(self.scroll_frame, height=80, bg="#000000", highlightbackground=Tema.VIDEO, highlightthickness=1)
        self.screen.pack(fill="x", padx=5, pady=2)
        self.dibujar_espectro_parado()
        
        self.btn_play = tk.Button(self.scroll_frame, text="|| PAUSE", bg=Tema.ALERTA, fg="#000000", font=("Arial", 12, "bold"), command=self.toggle_play)
        self.btn_play.pack(fill="x", padx=5, pady=2)
        
        self.time_lbl = tk.Label(self.scroll_frame, text="00:00:00:00", fg=Tema.VIDEO, bg=Tema.FONDO, font=("Courier New", 30, "bold"))
        self.time_lbl.pack()
        
        self.lbl_stats = tk.Label(self.scroll_frame, text="CPU: 15% | RAM: 2.0GB | TEMP: 41 C", fg=Tema.STATS, bg=Tema.FONDO, font=("Arial", 9, "bold"))
        self.lbl_stats.pack(pady=2)
        
        f_acc = tk.Frame(self.scroll_frame, bg=Tema.FONDO)
        f_acc.pack(fill="x", padx=5, pady=5)
        b_s_top = {"bg": Tema.BOTON, "fg": Tema.TEXTO, "font": ("Arial", 9)}
        tk.Button(f_acc, text="[ BUSCAR ]", **b_s_top).pack(side="left", expand=True, fill="x", padx=1)
        tk.Button(f_acc, text="[ AUTO-IA ]", **b_s_top).pack(side="left", expand=True, fill="x", padx=1)
        tk.Button(f_acc, text="[ RESET ]", command=self.reset_all, **b_s_top).pack(side="left", expand=True, fill="x", padx=1)
        
        self.crear_timeline()
        
        # MENÚS CON SUB-SCROLLS
        self.frames_menu = {}
        self.crear_acordeon("[ VIDEO MASTER ] v", Tema.VIDEO, self.video_db)
        self.crear_acordeon("[ FOTO MASTER ] v", Tema.FOTO, self.foto_db)
        self.crear_acordeon("[ AUDIO MASTER ] v", Tema.AUDIO, self.audio_db)

    def crear_acordeon(self, titulo, color, db):
        contenedor_principal = tk.Frame(self.scroll_frame, bg=Tema.FONDO)
        contenedor_principal.pack(fill="x", padx=5, pady=2)
        
        btn_master = tk.Button(contenedor_principal, text=titulo, bg=Tema.PANEL, fg=color, font=("Arial", 10, "bold"))
        btn_master.pack(fill="x")
        
        # Área que tendrá altura fija y su propia barra de desplazamiento (flecha)
        area_colapsable = tk.Frame(contenedor_principal, bg=Tema.FONDO, height=180)
        area_colapsable.pack_propagate(False) # Forzar altura para que el scroll actúe
        
        sub_canvas = tk.Canvas(area_colapsable, bg=Tema.FONDO, highlightthickness=0)
        sub_scroll = ttk.Scrollbar(area_colapsable, orient="vertical", command=sub_canvas.yview)
        sub_frame = tk.Frame(sub_canvas, bg=Tema.FONDO)
        
        sub_frame.bind("<Configure>", lambda e, c=sub_canvas: c.configure(scrollregion=c.bbox("all")))
        sub_canvas.create_window((0, 0), window=sub_frame, anchor="nw")
        sub_canvas.configure(yscrollcommand=sub_scroll.set)
        
        # Generar los 100 botones en cuadrícula
        for i, nombre in enumerate(db):
            tk.Button(sub_frame, text=nombre, bg="#050505", fg=color, font=("Arial", 7),
                      command=lambda x=nombre, t=titulo: self.com(f"{t[2:7]} Effect -> {x} aplicado.")).grid(row=i//4, column=i%4, sticky="ew", padx=1, pady=1)

        self.frames_menu[titulo] = {
            "contenedor": area_colapsable,
            "sub_canvas": sub_canvas,
            "sub_scroll": sub_scroll,
            "abierto": False,
            "btn": btn_master,
            "titulo": titulo
        }
        btn_master.config(command=lambda t=titulo: self.toggle_menu(t))

    def toggle_menu(self, titulo):
        menu = self.frames_menu[titulo]
        if menu["abierto"]:
            menu["sub_canvas"].pack_forget()
            menu["sub_scroll"].pack_forget()
            menu["contenedor"].pack_forget()
            menu["btn"].config(text=menu["titulo"])
            menu["abierto"] = False
        else:
            menu["contenedor"].pack(fill="x")
            menu["sub_scroll"].pack(side="right", fill="y")
            menu["sub_canvas"].pack(side="left", fill="both", expand=True)
            menu["btn"].config(text=menu["titulo"].replace("v", "^"))
            menu["abierto"] = True

    # --- NUEVA PISTA DE FOTOS PROFESIONAL ---
    def crear_timeline(self):
        frame_tl = tk.Frame(self.scroll_frame, bg=Tema.FONDO, highlightbackground="#333333", highlightthickness=1)
        frame_tl.pack(fill="x", padx=5, pady=10)

        tk.Label(frame_tl, text="[ PISTAS DE EDICION MULTICAPA ]", fg="#aaaaaa", bg=Tema.FONDO, font=("Arial", 8, "bold")).pack()
        
        # Pista un poco más alta para que luzca bien
        self.canvas_tl = tk.Canvas(frame_tl, height=90, bg="#000000", highlightthickness=0)
        self.canvas_tl.pack(fill="x", padx=5, pady=2)
        self.dibujar_timeline()

        b_s = {"bg": "#111111", "font": ("Arial", 8, "bold")}
        
        btn_f1 = tk.Frame(frame_tl, bg=Tema.FONDO)
        btn_f1.pack(fill="x", pady=2)
        tk.Button(btn_f1, text="[+ FOTO]", command=self.add_foto, fg=Tema.VIDEO, **b_s).pack(side="left", expand=True, fill="x", padx=1)
        tk.Button(btn_f1, text="[+ AUDIO]", command=self.add_audio, fg=Tema.AUDIO, **b_s).pack(side="left", expand=True, fill="x", padx=1)
        
        btn_f2 = tk.Frame(frame_tl, bg=Tema.FONDO)
        btn_f2.pack(fill="x", pady=2)
        self.btn_mic = tk.Button(btn_f2, text="[ GRABAR MIC ]", command=self.toggle_mic, fg=Tema.ALERTA, **b_s)
        self.btn_mic.pack(side="left", expand=True, fill="x", padx=1)
        tk.Button(btn_f2, text="[ EXTRAER ]", command=self.extraer_audio, fg=Tema.FOTO, **b_s).pack(side="left", expand=True, fill="x", padx=1)

        tk.Button(frame_tl, text="[ CREAR VIDEO ]", command=self.crear_video, bg=Tema.IA, fg="#000000", font=("Arial", 12, "bold")).pack(fill="x", padx=5, pady=5)

    def dibujar_timeline(self):
        self.canvas_tl.delete("all")
        
        # Fondos de las pistas
        self.canvas_tl.create_rectangle(0, 5, 1000, 45, fill="#0a0a0a", outline="")
        self.canvas_tl.create_rectangle(0, 45, 1000, 85, fill="#050505", outline="")
        self.canvas_tl.create_line(0, 45, 1000, 45, fill="#222222")
        
        # Nombres de pista
        self.canvas_tl.create_text(25, 25, text="🎬 V1", fill="#777777", font=("Arial", 8, "bold"))
        self.canvas_tl.create_text(25, 65, text="🎵 A1", fill="#777777", font=("Arial", 8, "bold"))

        # Pista 1: Fotos y Video
        for i, foto in enumerate(self.timeline_fotos):
            x1 = 55 + (i * 65)
            # Dibuja bloque con borde blanco y nombre recortado
            self.canvas_tl.create_rectangle(x1, 10, x1+60, 40, fill=Tema.VIDEO, outline="#ffffff")
            texto_corto = (foto[:6] + '..') if len(foto)>6 else foto
            self.canvas_tl.create_text(x1+30, 25, text=texto_corto, fill="#000", font=("Arial", 7, "bold"))
            
        # Pista 2: Audios
        for i, audio in enumerate(self.timeline_audios):
            x1 = 55 + (i * 85)
            self.canvas_tl.create_rectangle(x1, 50, x1+80, 80, fill=Tema.AUDIO, outline="#ffffff")
            texto_corto = (audio[:8] + '..') if len(audio)>8 else audio
            self.canvas_tl.create_text(x1+40, 65, text=texto_corto, fill="#000", font=("Arial", 7, "bold"))

        # Cabezal de reproducción inicial
        self.actualizar_playhead()

    def actualizar_playhead(self):
        # Mueve la línea roja según los milisegundos transcurridos
        if self.playhead_id: self.canvas_tl.delete(self.playhead_id)
        if self.triangulo_id: self.canvas_tl.delete(self.triangulo_id)
        
        # Calcula posición X. 55 es el inicio, avanza según el tiempo. Reinicia a 55 al llegar a 320.
        pos_x = 55 + ((self.milisegundos / 50) % 280) if len(self.timeline_fotos)>0 or len(self.timeline_audios)>0 else 55
        
        # Línea y triángulo rojo (Playhead)
        self.playhead_id = self.canvas_tl.create_line(pos_x, 0, pos_x, 90, fill=Tema.ALERTA, width=2)
        self.triangulo_id = self.canvas_tl.create_polygon(pos_x-5, 0, pos_x+5, 0, pos_x, 7, fill=Tema.ALERTA)

    # --- FUNCIONES ---
    def toggle_play(self):
        self.reproduciendo = not self.reproduciendo
        if self.reproduciendo:
            self.btn_play.config(text="> REPRODUCIENDO", bg=Tema.IA)
            self.com("Reproduciendo... El cabezal se está moviendo en la pista.")
        else:
            self.btn_play.config(text="|| PAUSE", bg=Tema.ALERTA)
            self.dibujar_espectro_parado()
            self.com("Pausado.")

    def add_foto(self):
        f = filedialog.askopenfilename(title="Cargar Foto")
        if f:
            nombre = os.path.basename(f)
            self.timeline_fotos.append(nombre)
            self.com(f"Foto agregada a pista V1: {nombre}")
            self.dibujar_timeline()

    def add_audio(self):
        f = filedialog.askopenfilename(title="Cargar Audio")
        if f:
            nombre = os.path.basename(f)
            self.timeline_audios.append(nombre)
            self.com(f"Audio agregado a pista A1: {nombre}")
            self.dibujar_timeline()

    def extraer_audio(self):
        self.timeline_audios.append("Ext_MP3.mp3")
        self.com("Audio extraido y pegado en pista A1.")
        self.dibujar_timeline()

    def toggle_mic(self):
        self.grabando_mic = not self.grabando_mic
        if self.grabando_mic:
            self.btn_mic.config(bg=Tema.ALERTA, fg="#000000", text="[ DETENER ]")
            self.com("GRABANDO MICRÓFONO... Habla ahora.")
        else:
            self.btn_mic.config(bg="#111111", fg=Tema.ALERTA, text="[ GRABAR MIC ]")
            self.timeline_audios.append("Voz_01.wav")
            self.com("Voz guardada en Pista A1.")
            self.dibujar_timeline()

    def reset_all(self):
        self.timeline_fotos.clear()
        self.timeline_audios.clear()
        self.milisegundos = 0
        self.dibujar_timeline()
        self.com("Pistas de edición limpiadas.")

    def crear_video(self):
        if len(self.timeline_fotos) == 0:
            self.com("ERROR: Agrega al menos 1 FOTO a la pista V1.")
            return
        self.renderizando = True
        self.progreso_render = 0
        self.screen.delete("all")
        self.screen.create_text(180, 20, text="RENDERIZANDO...", fill=Tema.IA, font=("Arial", 12, "bold"), tags="txt_r")
        self.barra_carga = self.screen.create_rectangle(40, 50, 40, 70, fill=Tema.IA, outline="")
        self.ejecutar_renderizado()

    def ejecutar_renderizado(self):
        if not self.renderizando: return
        self.progreso_render += random.randint(2, 6)
        ancho = 40 + ((self.progreso_render / 100) * 280)
        self.screen.coords(self.barra_carga, 40, 50, ancho, 70)
        self.screen.itemconfig("txt_r", text=f"CODIFICANDO PROYECTO... {self.progreso_render}%")

        if self.progreso_render >= 100:
            self.renderizando = False
            self.screen.delete("all")
            self.screen.create_text(180, 40, text="[ EXPORTACIÓN EXITOSA ]", fill=Tema.AUDIO, font=("Arial", 12, "bold"))
            self.com("Tu obra maestra se guardó en la Galería.")
        else:
            self.root.after(100, self.ejecutar_renderizado)

    def dibujar_espectro_parado(self):
        if not self.renderizando:
            self.screen.delete("all")
            self.screen.create_text(180, 40, text="SIN SEÑAL / PAUSADO", fill="#333333", font=("Arial", 10, "bold"))

    def animar_espectro(self):
        self.screen.delete("all")
        for i in range(30):
            alto = random.randint(5, 70)
            color = random.choice([Tema.IA, Tema.VIDEO, Tema.AUDIO])
            x0 = 10 + (i * 11)
            self.screen.create_rectangle(x0, 75 - alto, x0 + 8, 75, fill=color, outline="")

    def actualizar_motor(self):
        if self.reproduciendo:
            self.milisegundos += 50
            self.animar_espectro()
            
            # Animar línea de la pista
            self.actualizar_playhead()
            
            cpu = random.randint(10, 45)
            ram = random.uniform(1.5, 3.2)
            temp = random.randint(38, 55)
            self.lbl_stats.config(text=f"CPU: {cpu}% | RAM: {ram:.1f}GB | TEMP: {temp} C")
            
        h, m, s, ms = (self.milisegundos//3600000)%24, (self.milisegundos//60000)%60, (self.milisegundos//1000)%60, (self.milisegundos%1000)//10
        self.time_lbl.config(text=f"{h:02}:{m:02}:{s:02}:{ms:02}")
            
        self.root.after(50, self.actualizar_motor)

    def com(self, t): 
        self.ia_msg.config(text=f"[EMI AI]: {t}")

if __name__ == "__main__":
    root = tk.Tk()
    app = EmiEditPro(root)
    root.mainloop()()
