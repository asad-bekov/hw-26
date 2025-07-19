# Домашнее задание к занятию 4 «Работа с roles»

>Репозиторий: hw‑26
>Выполнил: Асадбеков Асадбек
>Дата выполнения: июль 2025

## Содержание
1. **[Описание](#описание)**  
2. **[Ссылки на репозитории](#ссылки-на-репозитории)**  
3. **[Структура репозитория](#структура-репозитория)**  
4. **[Примеры запуска](#примеры-запуска)**  
5. **[Вопросы и ответы](#вопросы-и-ответы)**  
6. **[Документация по ролям](#документация-по-ролям)**  
7. **[Скриншоты выполнения](#скриншоты-выполнения)**  
8. **[Автор](#автор)**  

---

## Описание
Проект автоматизирует установку **ClickHouse**, **Vector** и **LightHouse** с помощью Ansible.  
Каждый сервис оформлен отдельной ролью и подключается к playbook через `requirements.yml`.

---

## Ссылки на репозитории
| Компонент | Ссылка |
|-----------|--------|
| Vector role | <https://github.com/asad-bekov/vector-role> |
| LightHouse role | <https://github.com/asad-bekov/lighthouse-role> |
| Playbook | <https://github.com/asad-bekov/ansible-playbook> |

---

## Структура репозитория
```
ansible-playbook/
├── README.md
├── requirements.yml
├── site.yml
├── inventory/
│   └── prod.yml
└── screenshots/
    ├── 1.png
    ├── 2.png
    └── 3.png
```

---

## Примеры запуска

1. **Клонируем репозиторий**
    ```
    git clone https://github.com/asad-bekov/ansible-playbook.git
    cd ansible-playbook
    ```

2. **Устанавливаем роли**
    ```
    ansible-galaxy install -r requirements.yml --force
    ```

3. **Редактируем inventory** (`inventory/prod.yml`)
    ```yaml
    all:
      hosts:
        clickhouse-01:
          ansible_host: 127.0.0.1
          ansible_user: asad
    ```

4. **Запуск playbook**
    ```
    ansible-playbook -i inventory/prod.yml site.yml -c local -K
    ```
    `-K` — запрос пароля sudo.

---

## Пример файлов

**requirements.yml**
```yaml
---
- src: git@github.com:asad-bekov/vector-role.git
  scm: git
  version: "main"
  name: vector

- src: git@github.com:asad-bekov/lighthouse-role.git
  scm: git
  version: "main"
  name: lighthouse

- src: git@github.com:AlexeySetevoi/ansible-clickhouse.git
  scm: git
  version: "1.13"
  name: clickhouse
```

**site.yml**
```yaml
---
- hosts: all
  become: true
  roles:
    - clickhouse
    - vector
    - lighthouse
```

---

## Вопросы и ответы
| Вопрос | Ответ |
|--------|-------|
| Как добавить роль? | Добавить в `requirements.yml`, затем `ansible-galaxy install -r requirements.yml` |
| Где задавать переменные? | В `defaults/main.yml` роли или через `vars` в playbook |
| Что такое идемпотентность? | Повторный запуск не меняет уже корректно настроенную систему |
| Как создать новую роль? | `ansible-galaxy init my-role` → git push в отдельный репозиторий |

---

## Документация по ролям

### Vector role (кратко)
| Переменная | Значение по умолчанию | Описание |
|------------|-----------------------|----------|
| `vector_data_dir` | `/var/lib/vector` | Директория данных |
| `vector_source_type` | `stdin` | Источник логов |
| `vector_transform_type` | `json_parser` | Трансформация |
| `vector_sink_type` | `console` | Приёмник |
| `vector_codec` | `json` | Кодек |

Пример:
```yaml
- hosts: all
  roles:
    - role: vector-role
      vars:
        vector_sink_type: console
```

### LightHouse role (кратко)
| Переменная | Значение | Описание |
|------------|----------|----------|
| `lighthouse_repo` | <https://github.com/VKCOM/lighthouse.git> | Репозиторий |
| `lighthouse_dest` | `/opt/lighthouse` | Путь установки |

Пример:
```yaml
- hosts: all
  roles:
    - role: lighthouse-role
```

---

## Скриншоты выполнения
Скриншоты лежат в папке `img/`. Ниже примеры размещения:

| Шаг | Превью |
|-----|--------|
| Установка ролей | ![](./img/1.png) |
| Выполнение playbook | ![](./img/2.png) |

---

## Автор
Asad Asadbekov