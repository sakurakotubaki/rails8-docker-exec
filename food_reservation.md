# FoodReservation 一覧表示手順

## 1. ルーティング設定

`myapp/config/routes.rb` にリソースルートを追加します。

```ruby
resources :food_reservations, only: [:index]
```

---

## 2. コントローラー作成

`myapp/app/controllers/food_reservations_controller.rb` に index アクションを定義します。

```ruby
class FoodReservationsController < ApplicationController
  def index
    @food_reservations = FoodReservation.all.order(reservation_date: :desc)
  end
end
```

---

## 3. ビュー作成

`myapp/app/views/food_reservations/index.html.erb` に一覧表示用のHTMLを記述します。

```erb
<h1>Food Reservations</h1>
<table>
  <thead>
    <tr>
      <th>ID</th>
      <th>Customer Name</th>
      <th>Reservation Date</th>
      <th>Number of Guests</th>
      <th>Special Requests</th>
    </tr>
  </thead>
  <tbody>
    <% @food_reservations.each do |reservation| %>
      <tr>
        <td><%= reservation.id %></td>
        <td><%= reservation.customer_name %></td>
        <td><%= reservation.reservation_date %></td>
        <td><%= reservation.number_of_guests %></td>
        <td><%= reservation.special_requests %></td>
      </tr>
    <% end %>
  </tbody>
</table>
```

---

## 4. アクセスURL

```
http://localhost:3000/food_reservations
```

このURLで一覧が表示されます。