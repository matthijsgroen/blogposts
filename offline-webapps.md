Introduction
============

HTML 5 makes it possible to run your webapplications event when you're
offline. But how easy is it to create an app that makes use of this?

In this blogpost I will build a simple Rails 4 application, that
performs some CRUD actions. Add a backbone layer on top of that and make
sure my App functions when there is no internet connection.

Building the Rails app
======================

Open your favorite terminall shell and we can start building

```sh
mkdir backbone-offline
cd backbone-offline
gem install rails --version=4.0.0
rails new .
```

Now we have our typically Rails webapp skeleton, and now we want some
simple CRUD objects to play around with later:

```sh
rails generate scaffold hotel_room name:string
rails generate scaffold hotel_booking arrival_date:date departure_date:date hotel_room:belongs_to
rake db:create
rake db:migrate
rails s
```

We can now visit our rails bookings overview by visitig:

[http://localhost:3000/hotel_bookings/](http://localhost:3000/hotel_bookings/)

Pretty boring so far, but let's make sure our app is functioning before
we continue to make our application into a single-page app.

First we create a seed in `db/seeds.rb`:

```ruby
['Bridal Suite', 'Business Suite', 'Family Suite'].each do |room_name|
  HotelRoom.create name: room_name
end
```

Now we change our text input for the hotel room into a select:

```erb
<div class="field">
  <%= f.label :hotel_room_id %><br>
  <%= f.collection_select :hotel_room_id, HotelRoom.all, :id, :name %>
</div>
```

You can also change the other views to display the correct room name
instead of the printout of the HotelRoom object instance.

We add now some backend-business rules to our HotelBooking model:

```ruby
validates :hotel_room_id, presence: true
validate :departure_date_must_be_after_arrival_date
validate :arrival_date_must_be_in_the_future

def departure_date_must_be_after_arrival_date
  if departure_date.present? && arrival_date.present? && departure_date <= arrival_date
    errors.add(:departure_date, 'must be after %s' % self.class.human_attribute_name(:arrival_date))
  end
end

def arrival_date_must_be_in_the_future
  if arrival_date.present? && !arrival_date.future?
    errors.add(:arrival_date, 'must be in the future')
  end
end
```

Now we only need to prevent double bookings for the same room. Which can
only be validated by the backend (The earlier validations can also be
done in a full front-end fashion).

```ruby
validate :room_must_not_be_booked_already

def room_must_not_be_booked_already
  if hotel_room_id.present? && arrival_date.present? && departure_date.present?
    arrival_date_field = self.class.arel_table[:arrival_date]
    departure_date_field = self.class.arel_table[:departure_date]

    room_booked_query = self.class.
      where(hotel_room_id: hotel_room_id).
      where(arrival_date_field.lt(departure_date)).
      where(departure_date_field.gt(arrival_date))

    errors.add(:hotel_room_id, 'is already booked in the requested period') if room_booked_query.any?
  end
end
```

# Making the app into a single-page app

The next step is to add a front-end layer on top of our current rails
app. I use Backbone for this purpose. Since this post is not so much
about making a single page app in Backbone, I suggest to checkout the
commits I made to change the Rails app into a Backbone app.


