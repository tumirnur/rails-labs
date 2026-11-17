# Отчёт по лабораторным работам: Ruby on Rails Tutorial, главы 1–2

**Студентка:** Тенишева Элина Тимуровна, группа 4411
**Дата:** 17.11.2026
**Репозиторий:** (ссылка появится после git push)

## Окружение


```text
$ ruby -v
ruby 4.0.9 (2026-10-23 revision 3e93b23afc) +PRISM [arm64-darwin25]
$ rails -v
Rails 8.1.4.1
$ git --version
git version 2.50.1 (Apple Git-155)
$ sw_vers -productVersion
26.5.2

```

Книга написана под Rails 4.2.2, который не запускается на современном Ruby, поэтому работа выполнена на актуальной версии Rails (8.1.4.1). Отличия от книги:

- `render text:` удалён из Rails → используется `render html:`;
- модели наследуются от `ApplicationRecord` (а он — от `ActiveRecord::Base`);
- миграции запускаются `bin/rails db:migrate` вместо `bundle exec rake db:migrate`;
- код хранится на GitHub вместо Bitbucket;
- развёртывание на Heroku не выполнялось: у Heroku больше нет бесплатного тарифа.

---

# Часть 1. От нуля к развёртыванию (hello_app)

## 1. Создание приложения

Команда `rails new hello_app` создала стандартную структуру Rails-приложения (app, config, db, test и т.д.) и установила гемы через Bundler. После запуска `rails server` открылась стандартная стартовая страница Rails.

> Скриншот «Стартовая страница Rails» — вставить вручную (docs/screenshots/hello_01_rails_default.png)

## 2. Hello, world! (MVC)

В `ApplicationController` добавлено действие `hello`, а в `config/routes.rb` корневой маршрут `root "application#hello"` направлен на это действие.

```ruby
class ApplicationController < ActionController::Base
  def hello
    render html: "hello, world!"
  end
end
```

> Скриншот «Корневая страница выводит «hello, world!»» — вставить вручную (docs/screenshots/hello_02_hello_world.png)

## 3. Управление версиями с Git

Репозиторий создан командой `git init`, каждый шаг зафиксирован отдельным коммитом и отправлен на GitHub (`git push`).

Для проверки восстановления файлов каталог `app/controllers` был удалён и восстановлен командой `git checkout -f`:

```text
$ ls hello_app/app/controllers/
application_controller.rb
concerns
$ rm -rf hello_app/app/controllers/
$ git status --short
 D hello_app/app/controllers/application_controller.rb
 D hello_app/app/controllers/concerns/.keep
$ git checkout -f
$ ls hello_app/app/controllers/
application_controller.rb
concerns

```

README приложения изменён в отдельной ветке `modify-README`: ветвление → редактирование → коммит → слияние с `main` → удаление ветки:

```text
$ git checkout -b modify-README
Switched to a new branch 'modify-README'
$ git branch
  main
* modify-README
$ git status --short
 M hello_app/README.md
$ git commit -a -m "hello_app: improve the README file"
[modify-README abac784] hello_app: improve the README file
 1 file changed, 5 insertions(+), 22 deletions(-)
$ git checkout main
Switched to branch 'main'
$ git merge modify-README
Updating 071e782..abac784
Fast-forward
 hello_app/README.md | 27 +++++----------------------
 1 file changed, 5 insertions(+), 22 deletions(-)
$ git branch -d modify-README
Deleted branch modify-README (was abac784).

```

## 4. Упражнения

1. Действие `hello` изменено на вывод «¡Hola, mundo!» — проверено, что Rails корректно выводит не-ASCII символы.

> Скриншот «Вывод «¡Hola, mundo!»» — вставить вручную (docs/screenshots/hello_03_hola_mundo.png)

2. Добавлено действие `goodbye`, корневой маршрут переключён на него (`root "application#goodbye"`).

> Скриншот «Корневой маршрут ведёт на goodbye» — вставить вручную (docs/screenshots/hello_04_goodbye_world.png)

---

# Часть 2. Мини-приложение (toy_app)

## 1. Планирование и модели данных

Создано приложение `toy_app` (микроблог). Модели данных:

| users | | microposts | |
|---|---|---|---|
| id | integer | id | integer |
| name | string | content | text |
| email | string | user_id | integer |

## 2. Ресурс Users

Командой `rails generate scaffold User name:string email:string` сгенерированы модель, миграция, контроллер, представления и маршруты `resources :users`. Миграция применена `bin/rails db:migrate`, корневой маршрут — `root "users#index"`.

> Скриншот «Пустой список пользователей (/users)» — вставить вручную (docs/screenshots/toy_01_users_index_empty.png)


> Скриншот «Страница создания пользователя (/users/new)» — вставить вручную (docs/screenshots/toy_02_users_new.png)


> Скриншот «Созданный пользователь (/users/1)» — вставить вручную (docs/screenshots/toy_03_user_show.png)


> Скриншот «Редактирование пользователя (/users/1/edit)» — вставить вручную (docs/screenshots/toy_04_user_edit.png)


> Скриншот «Пользователь после обновления» — вставить вручную (docs/screenshots/toy_05_user_updated.png)


> Скриншот «Список из двух пользователей» — вставить вручную (docs/screenshots/toy_06_users_index_two.png)


> Скриншот «Список после удаления второго пользователя» — вставить вручную (docs/screenshots/toy_07_users_after_destroy.png)

REST-маршруты ресурсов (`bin/rails routes`):

```text
   Prefix Verb   URI Pattern               Controller#Action
    users GET    /users(.:format)          users#index
          POST   /users(.:format)          users#create
 new_user GET    /users/new(.:format)      users#new
edit_user GET    /users/:id/edit(.:format) users#edit
     user GET    /users/:id(.:format)      users#show
          PATCH  /users/:id(.:format)      users#update
          PUT    /users/:id(.:format)      users#update
          DELETE /users/:id(.:format)      users#destroy
     root GET    /                         users#index
        Prefix Verb   URI Pattern                    Controller#Action
    microposts GET    /microposts(.:format)          microposts#index
               POST   /microposts(.:format)          microposts#create
 new_micropost GET    /microposts/new(.:format)      microposts#new
edit_micropost GET    /microposts/:id/edit(.:format) microposts#edit
     micropost GET    /microposts/:id(.:format)      microposts#show
               PATCH  /microposts/:id(.:format)      microposts#update
               PUT    /microposts/:id(.:format)      microposts#update
               DELETE /microposts/:id(.:format)      microposts#destroy

```

**MVC на примере `/users`:** маршрутизатор направляет запрос в `UsersController#index` → контроллер получает всех пользователей `User.all` из модели → передаёт их в переменной `@users` в представление `index.html.erb` → представление формирует HTML, который возвращается браузеру.

## 3. Ресурс Microposts

Сгенерирован scaffold `Micropost content:text user_id:integer`, созданы два микропоста пользователя с id = 1.

> Скриншот «Страница создания микропоста» — вставить вручную (docs/screenshots/toy_08_micropost_new.png)


> Скриншот «Список микропостов (/microposts)» — вставить вручную (docs/screenshots/toy_09_microposts_index.png)

Добавлено ограничение длины микропоста — 140 символов:

```ruby
class Micropost < ApplicationRecord
  validates :content, length: { maximum: 140 }
end
```

> Скриншот «Ошибка валидации: микропост длиннее 140 символов» — вставить вручную (docs/screenshots/toy_10_micropost_too_long.png)

## 4. Связь «у пользователя много микропостов»

```ruby
class User < ApplicationRecord
  has_many :microposts
end

class Micropost < ApplicationRecord
  belongs_to :user
  validates :content, length: { maximum: 140 }
end
```

Проверка в консоли Rails (`bin/rails runner`), там же видна иерархия наследования моделей и контроллеров:

```text
>> first_user = User.first
#<User id: 1, name: "Элина Тенишева (гр. 4411)", email: [FILTERED], created_at: "2026-11-17 20:05:40.604067000 +0000", updated_at: "2026-11-17 20:05:40.682470000 +0000">
>> first_user.microposts
[#<Micropost id: 1, content: "Мой первый микропост!", user_id: 1, created_at: "2026-11-17 20:05:42.809279000 +0000", updated_at: "2026-11-17 20:05:42.809279000 +0000">, #<Micropost id: 2, content: "Второй микропост: изучаю Ruby on Rails", user_id: 1, created_at: "2026-11-17 20:05:42.864113000 +0000", updated_at: "2026-11-17 20:05:42.864113000 +0000">]
>> micropost = first_user.microposts.first
#<Micropost id: 1, content: "Мой первый микропост!", user_id: 1, created_at: "2026-11-17 20:05:42.809279000 +0000", updated_at: "2026-11-17 20:05:42.809279000 +0000">
>> micropost.user
#<User id: 1, name: "Элина Тенишева (гр. 4411)", email: [FILTERED], created_at: "2026-11-17 20:05:40.604067000 +0000", updated_at: "2026-11-17 20:05:40.682470000 +0000">
>> [User.superclass, User.superclass.superclass]
[ApplicationRecord(abstract), ActiveRecord::Base]
>> [UsersController.superclass, UsersController.superclass.superclass]
[ApplicationController, ActionController::Base]

```

**Иерархия наследования:** `User`, `Micropost` → `ApplicationRecord` → `ActiveRecord::Base`; `UsersController`, `MicropostsController` → `ApplicationController` → `ActionController::Base`.

## 5. Упражнения

1. Валидация наличия содержимого микропоста (`presence: true`):

> Скриншот «Ошибка: пустой микропост» — вставить вручную (docs/screenshots/toy_11_micropost_blank.png)

2. Валидации наличия имени и email у пользователя:

```ruby
class User < ApplicationRecord
  has_many :microposts
  validates :name,  presence: true
  validates :email, presence: true
end
```

> Скриншот «Ошибка: пустые имя и email» — вставить вручную (docs/screenshots/toy_12_user_blank.png)

---

# Репозиторий и история коммитов

Ссылка: (ссылка появится после git push)

> Скриншот «Репозиторий на GitHub» — вставить вручную (docs/screenshots/github_repo.png)

- `86bc131` Initialize repository
- `2417959` hello_app: generate Rails 8.1.4.1 application
- `071e782` hello_app: add hello action and root route
- `abac784` hello_app: improve the README file
- `1df6c70` hello_app: exercise - ¡Hola, mundo!
- `47a0e53` hello_app: exercise - goodbye action as root route
- `b4b89dc` docs: part 1 screenshots and logs
- `f1a3861` toy_app: generate Rails application
- `c2f1f90` toy_app: add hello action
- `095697a` toy_app: scaffold Users resource
- `9282003` toy_app: route root to users#index
- `4c1b6b1` toy_app: scaffold Microposts resource
- `0cada02` toy_app: limit micropost length to 140 characters
- `7793646` toy_app: user has many microposts
- `5fe6c10` docs: console session and routes for toy_app
- `aa57eb1` toy_app: exercise - validate micropost content presence
- `7dc373e` toy_app: exercise - validate user name and email presence
- … `docs: add lab report`

# Вывод

Освоены создание Rails-приложений, структура MVC, маршрутизация, работа с Git (коммиты, ветки, слияние, удалённый репозиторий), генерация REST-ресурсов через scaffold, миграции базы данных, валидации и связи моделей `has_many` / `belongs_to`.
