Меня зовут Усманов Олег Русланович я только учусь на Junior-разработчик
openapi: 3.0.0
info:
  title: API для управления товарами и заказами
  description: получение списка, детальная информация, добавление и обновление
  version: 1.0.0
components:
  schemas:
    Product:
      type: object
      required:
        - name
        - price
      properties:
        id:
          type: integer
          format: int64
          description: Уникальный идентификатор товара
          example: 592650
        name:
          type: string
          description: Название товара
          example: "Холодильник двухкамерный Самсунг"
        description:
          type: string
          description: Описание товара
          example: "Холодильник с функцией No Frost"
        price:
          type: number
          format: float
          description: Цена товара
          example: 49999.99
        category:
          type: string
          description: Категория товара
          example: "Бытовая техника"
        stock_quantity:
          type: integer
          description: Количество на складе
          example: 20
        is_active:
          type: boolean
          description: Активен ли товар (доступен для продажи)
          example: true
    Error:
      type: object
      properties:
        code:
          type: integer
          example: 404
        message:
          type: string
          example: "Товар не найден"
  responses:
    NotFound:
      description: Товар не найден
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'

    BadRequest:
      description: Некорректные данные
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
paths:
  /products:
    get:
      summary: Получить список всех товаров
      description: Возвращает список всех доступных товаров
      operationId: getProducts
      tags:
        - products
      parameters:
        - name: page
          in: query
          description: Номер страницы
          required: false
          schema:
            type: integer
            minimum: 1
            default: 1
        - name: limit
          in: query
          description: Количество товаров на странице (по умолчанию 20, максимум 100)
          required: false
          schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 20
      responses:
        '200':
          description: Успешный ответ с списком товаров
          content:
            application/json:
              schema:
                type: object
                properties:
                  total:
                    type: integer
                    example: 150
                  page:
                    type: integer
                    example: 1
                  limit:
                    type: integer
                    example: 20
                  items:
                    type: array
                    items:
                      $ref: '#/components/schemas/Product'
        '500':
          description: Внутренняя ошибка сервера
  /products/:
    get:
      summary: Получить информацию о конкретном товаре по его ID
      description: Возвращает полную информацию о конкретном товаре
      operationId: getProductById
      tags:
        - products
      parameters:
        - name: id
          in: path
          description: ID товара
          required: true
          schema:
            type: integer
            format: int64
      responses:
        '200':
          description: Информация о товаре
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Product'
        '404':
          $ref: '#/components/responses/NotFound'
        '500':
          description: Внутренняя ошибка сервера
    post:
      summary: Добавить новый товар
      description: Создаёт новый товар в каталоге
      operationId: createProduct
      tags:
        - products
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Product'
      responses:
        '201':
          description: Товар успешно создан
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Product'
        '404':
          $ref: '#/components/responses/NotFound'
        '500':
          description: Ошибка при создании товара

    put:
      summary: Обновить информацию о существующем товаре
      description: Обновляет данные каталога
      operationId: updateProduct
      tags:
        - products
      parameters:
        - name: id
          in: path
          description: ID товара для обновления
          required: true
          schema:
            type: integer
            format: int64
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Product'
      responses:
        '200':
          description: Товар успешно обновлён
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Product'
        '400':
          description: Некорректные данные
        '404':
          $ref: '#/components/responses/NotFound'
        '500':
          description: Ошибка при обновлении товара

    delete:
     summary: Удалить товар
     description: Удаляет товар из каталога
     operationId: deleteProduct
     tags:
       - products
     parameters:
       - name: id
         in: path
         description: ID товара для удаления
         required: true
         schema:
           type: integer
           format: int64
     responses:
       '204':
         description: Товар успешно удалён (нет содержимого)
       '404':
         $ref: '#/components/responses/NotFound'
       '500':
         description: Ошибка сервера

