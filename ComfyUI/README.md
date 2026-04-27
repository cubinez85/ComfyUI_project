📋 Проект: ComfyUI — Локальная генерация изображений
🎯 Описание проекта
ComfyUI — это мощный инструмент для генерации изображений на базе Stable Diffusion с узловым (нодовым) интерфейсом.
В отличие от других оболочек, ComfyUI даёт полный контроль над процессом генерации через визуальное программирование.
🏗️ Архитектура системы
┌─────────────────────────────────────────────────────────────┐
│                      Windows 11 (Хост)                       │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │                    WSL-2 (Ubuntu 22.04)                  │ │
│  │  ┌─────────────────────────────────────────────────────┐│ │
│  │  │              ComfyUI Service (systemd)               ││ │
│  │  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐ ││ │
│  │  │  │   Python    │  │   PyTorch   │  │   Models    │ ││ │
│  │  │  │   3.10.12   │  │   2.4.0+cpu │  │  checkpoints│ ││ │
│  │  │  └─────────────┘  └─────────────┘  └─────────────┘ ││ │
│  │  │         │                │                │         ││ │
│  │  │         └────────────────┴────────────────┘         ││ │
│  │  │                      │                              ││ │
│  │  │              Порт: 127.0.0.1:8188                   ││ │
│  │  └─────────────────────────────────────────────────────┘│ │
│  └─────────────────────────────────────────────────────────┘ │
│                           │                                  │
│                    Браузер: http://localhost:8188            │
└─────────────────────────────────────────────────────────────┘
📁 Структура папок
/home/cubinez85/
├── ComfyUI/                    # Основная директория
│   ├── models/                 # Модели (checkpoints, LoRA, VAE)
│   │   ├── checkpoints/        # Основные модели (.ckpt, .safetensors)
│   │   ├── loras/              # LoRA-адаптеры
│   │   ├── vae/                # VAE-модели
│   │   └── upscale_models/     # Модели апскейла
│   ├── output/                 # Сгенерированные изображения ✅
│   ├── input/                  # Входные изображения (img2img)
│   ├── custom_nodes/           # Пользовательские ноды
│   └── user/                   # Сохранённые воркфлоу
├── venv/                       # Виртуальное окружение Python
└── comfyui.service             # Systemd-сервис

📁 Описание папки wheels/ в ComfyUI
Эта папка содержит локальные установочные пакеты (кэш) для Python-библиотек.
Она используется для установки зависимостей без скачивания из интернета.
📦 Что внутри:
   Файл	                                                       Описание	                               Размер (примерно)
**`torch-2.3.1+cu121-cp310-cp310-linux_x86_64.whl`**	       PyTorch с поддержкой **CUDA 12.1**	~2 ГБ
**`torchvision-0.18.1+cu121-cp310-cp310-linux_x86_64.whl`**    TorchVision (работа с изображениями)	~150 МБ
**`torchaudio-2.3.1+cu121-cp310-cp310-linux_x86_64.whl`**      TorchAudio (работа со звуком)	        ~50 МБ
**`cuda-keyring_1.1-1_all.deb`**	                       Ключ репозитория NVIDIA CUDA для apt	~10 КБ
💡 Зачем эта папка нужна:
    Преимущество	   Описание
**🚀 Офлайн-установка**	   Можно установить PyTorch без интернета
**⚡ Быстрая установка**   Не нужно скачивать 2+ ГБ каждый раз
** Стабильность**	   Фиксированная версия, не обновится случайно
**💾 Экономия трафика**	   Один раз скачал — используешь многократно
🛠 Как использовать эти файлы:
cd ~/ComfyUI/wheels
pip install torch-2.3.1+cu121-cp310-cp310-linux_x86_64.whl
pip install torchvision-0.18.1+cu121-cp310-cp310-linux_x86_64.whl
pip install torchaudio-2.3.1+cu121-cp310-cp310-linux_x86_64.whl
⚠️ Важные моменты:
CUDA 12.1 = Нужен драйвер NVIDIA
# Проверьте версию драйвера
nvidia-smi


⚙️ Технические характеристики
Параметр	Значение
**OS**	        Ubuntu 22.04 (WSL-2 на Windows 11)
**Python**	3.10.12
**PyTorch**	2.4.0+cpu
**ComfyUI**	0.18.1
**Порт**	127.0.0.1:8188
**RAM**	        7.8 ГБ
**Swap**	8.0 ГБ
**Устройство**	CPU (режим `--lowvram`)
**Сервис**	`systemd` (автозапуск)

Увеличение оперативной памяти и swap подкачки
# 1. Создайте swap-файл 8 ГБ
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# 2. Сделайте постоянным (чтобы работал после перезагрузки)
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

# 3. Проверьте
free -h
# Swap: должно быть ~8.0G ✅

# Проверить текущее значение
cat /proc/sys/vm/swappiness

# Временно установить 10 (до перезагрузки)
sudo sysctl vm.swappiness=10

# Постоянно: добавить в /etc/sysctl.conf
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

notepad C:\Users\Oleg\.wslconfig

[wsl2]
memory=8GB
swap=0
localhostForwarding=true

wsl --shutdown

📈 Мониторинг памяти во время генерации
# Откройте новый терминал WSL и следите:
watch -n 2 'free -h' 

Показатель,Норма,Тревога
Mem used,< 6 ГБ,> 7.5 ГБ
Swap used,0-2 ГБ,> 5 ГБ
Available,> 500 МБ,< 100 МБ

💡 Советы для стабильной работы
Закройте лишнее в Windows — браузеры с кучей вкладок едят RAM
Не запускайте другие тяжёлые программы во время генерации
Используйте профиль 256×256 — лучший баланс качество/скорость/память
Следите за температурой — CPU будет нагружен 100% во время генерации
Делайте перерывы — дайте системе остыть между генерациями

🎨 Примеры промптов для генерации
1. Минимальный тест (128×128, 5 steps)
{
  "prompt": "red circle",
  "negative": "bad quality, blurry",
  "width": 128,
  "height": 128,
  "steps": 5,
  "cfg": 7,
  "sampler": "euler",
  "seed": 12345
}
2. Пейзаж (256×256, 15 steps)
{
  "prompt": "sunset over mountains, lake reflection, golden hour, detailed, 4k",
  "negative": "ugly, blurry, low quality, deformed",
  "width": 256,
  "height": 256,
  "steps": 15,
  "cfg": 7.5,
  "sampler": "dpmpp_2m",
  "seed": -1
}
3. Портрет (512×512, 20 steps)
{
  "prompt": "portrait of a young woman, long hair, blue eyes, soft lighting, detailed face, photorealistic",
  "negative": "ugly, deformed, noisy, blurry, low contrast, extra fingers",
  "width": 512,
  "height": 512,
  "steps": 20,
  "cfg": 7,
  "sampler": "dpmpp_2m_karras",
  "seed": -1
}
Фэнтези (512×768, 30 steps)
{
  "prompt": "fantasy landscape, castle on a hill, dragon flying, magical aura, epic, detailed, artstation",
  "negative": "ugly, deformed, noisy, blurry, low quality",
  "width": 512,
  "height": 768,
  "steps": 30,
  "cfg": 7.5,
  "sampler": "dpmpp_2m_karras",
  "seed": -1
}
Параметры генерации
Параметр	        Описание	                Рекомендации
**Prompt**	 Описание того, что рисовать	     Чем детальнее, тем лучше
**Negative**	 Что НЕ рисовать	             Всегда используйте для качества
**Width/Height** Разрешение изображения	             128-512 для CPU
**Steps**	 Количество итераций	             5-30 (больше = лучше, но медленнее)
**CFG Scale**	 Соответствие промпту	             7-8 (оптимально)
**Sampler**	 Алгоритм сэмплирования	             `dpmpp_2m_karras` — лучший баланс
**Seed**	 Случайное число	                     `-1` = случайное, число = фиксированное

🛠️ Полезные команды
# Проверка памяти
free -h

# Проверка места на диске
df -h

# Мониторинг во время генерации
watch -n 2 'free -h'

# Поиск сгенерированных файлов
ls -la ~/ComfyUI/output/

# Очистка старых изображений
rm ~/ComfyUI/output/*.png

