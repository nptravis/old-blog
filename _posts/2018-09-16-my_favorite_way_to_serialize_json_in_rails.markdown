---
layout: post
title:      "My Favorite way to serialize JSON in Rails"
date:       2018-09-16 11:11:19 -0400
permalink:  my_favorite_way_to_serialize_json_in_rails
---



## Action Model Serializer

There are a few ways to do this: to_json, fast_jsonapi is a new one, but my favorite is action_model_serializer. Why? Well, i spent many hours banging my head against other ways, using to_json, pouring over the documentation, following examples exactly and still couldnt get it, even resorted to manually serailizing. Then I found it, the one, active_model_serializer. Want to easily attach nested associations? Want to use one serializer for single models and associations? Want to pass in the exact same html from your show view? Here is how it works. 

first, install the gem:

```
gem 'active_model_serializers', '~> 0.10.2'
bundle install
```

Next generate a serailizer based on an existing model:
```
rails g serializer Product
```

this creates this:
```
<!-- app/serializers/post_serializer.rb -->
class ProductSerializer < ActiveModel::Serializer
  attributes :id
end

```
Now you can add other attributes and associations:
```
class ProductSerializer < ActiveModel::Serializer
  attributes :id, :name, :price, :category
  belongs_to :brand
end
```
For the association to work you must add another serializer for brand:

EDIT: Actually, you don't need the second serializer for it to work, but this will allow you to customize the association sent, trimming back attributes that aren't needed, custom methods, etc....
```
rails g serializer Brand
```
and in that serializer:
```
class BrandSerializer < ActiveModel::Serializer
 attributes: :id, :name, :motto
 has_many :products
end
```

Add a nested association, but again must add a serializer for users and reviews:

EDIT: again not needed, but recommended.
```
class ProductSerializer < ActiveModel::Serializer
  attributes :id, :name, :price, :category
  belongs_to :brand
  has_many :reviews
  has_many :users, through: :reviews
end
```

Now, all of that will be sent via serailized JSONt. Cool right? But... we're not finished yet, in your controller:
```
def show
	@product = Product.find(params[:id])
	render json: @product, serializer: ProductSerializer
end
```
That's it, send an api call to '/products/:id' and you'll get all the product data, the brand, the reviews, and users. Now, to show an index of all products, with all this data available for each one:

```
def index
		@products = Product.all
		render json: @products, each_serailzer: ProductSerializer
	end
```

All products sent will have all the same data.

You can also send the entire show view:
```
class ProductSerializer < ActiveModel::Serializer
  attributes :id, :name, :price, :category

  attributes :show

  belongs_to :brand
  has_many :reviews
  has_many :users, through: :reviews

  def show
  	ProductsController.render(:show, assigns: {product: object}, layout: false)
  end
end
```

This is rendering the view associated with the ProductsController action. this line:
```
assigns: {product: object}
```
is setting the product instance variable as the object that the serializer is called upon. Basically, in the serializer, object represents self. 

You can also customize these methods any way you want:
```
class ProductSerializer < ActiveModel::Serializer
  attributes :id, :name, :price, :category

  attributes :show
  attributes :all_current_orders

  belongs_to :brand
  has_many :reviews
  has_many :users, through: :reviews

  def show
  	ProductsController.render(:show, assigns: {product: object}, layout: false)
  end

  def all_current_orders
  	object.orders.select {|order| order.created_at >= Time.now - 5.days }
  end
end
```

## In Conclusion

I struggled for a while with to_json, which is still good and fast for simple direct associations OR methods. I never got it to work correctly with associations AND methods. It just didn't seem as flexible, and it says on the rails API dock it may be depricated. Although, as of this writing, the active_record_serializers gem is still being developed, it's working swimmingly so far. Thumbs up! 
