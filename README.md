DevSecOps Demo Repository

Опис
Це навчальний репозиторій для демонстрації базових DevSecOps-практик:
- Імпорт уразливого прикладу коду (`src/41_scan_stream_default.py`).
- Автоматична збірка та реліз через GitHub Actions.
- Підпис артефакту за допомогою Cosign (keyless OIDC).
- Генерація SBOM (Syft) та перевірка SBOM (Grype).
- Перевірка безпеки репозиторію (OpenSSF Scorecard).
- Гілки `dev`, `stage`, `main` з політикою внесення змін лише через Merge Request в `main`.

Алгоритм виконання (Tech Writer)
1) Підготовка репозиторію
   - Створити на GitHub приватний/публічний репозиторій.
   - Клонувати локально та додати файли з цього прикладу.
   - Додати чотирьох користувачів до репозиторію через Settings → Collaborators; лише одному надати право push у `main` або повністю заборонити прямий push через Branch protection (рекомендовано).

2) Гілки та політики
   - Створити гілки: `dev`, `stage`, `main`.
   - Увімкнути Branch protection для `main`:
     - Require a pull request before merging.
     - Require status checks to pass (build, scorecard, grype).
     - Restrict who can push (або взагалі заборонити прямий push).

3) Додавання коду
   - Скопіювати файл `41_scan_stream_default.py` із репозиторію `savostyanenko/example_of_vulnerable_code_2` у `src/`.
   - Оновити `requirements.txt` лише в разі реальної потреби (жодних зайвих залежностей).

4) Збірка і реліз
   - Пайплайн GitHub Actions:
     - Checkout → Python setup → Збірка «псевдобінаря» (zip) через `make build`.
     - Генерація SBOM (Syft) → підпис артефакту Cosign (keyless) → завантаження у реліз 1.0.0.
     - Збереження build-артефактів як artifacts.

5) Перевірки безпеки
   - OpenSSF Scorecard: workflow запускається на `push`/`schedule`, публікує результати як артефакти.
   - Grype: запускається на `push`/`pull_request`, сканує SBOM і фейлить білд у разі критичних вразливостей (політика за потребою).

6) Ролі команди
   - PO: створює таски за цим завданням, разом із командою оцінює їх у Planning Poker (`https://planningpokeronline.com/`); пріоритизує, якщо задач більше ніж уміщається в спринт.
   - Dev/DevOps: виконують задачі, оновлюють пайплайни, фіксять перевірки.
   - Tech Writer: документує процес, алгоритм, бере участь у всіх активностях команди.

Локальні команди (приклад)
```bash
git init
git branch -M main
mkdir -p src
# завантажити файл у src/41_scan_stream_default.py
make build
```

Реліз 1.0.0
- Створіть тег `v1.0.0` і пушніть у репозиторій. Пайплайн збере артефакт, підпише Cosign, додасть SBOM і створить GitHub Release.

Перевірки
- Scorecard запускається автоматично.
- Grype запускається автоматично і перевіряє SBOM.


