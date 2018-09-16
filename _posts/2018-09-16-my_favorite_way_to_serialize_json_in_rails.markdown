---
layout: post
title:      "My Favorite way to serialize JSON in Rails"
date:       2018-09-16 15:11:18 +0000
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
rails g serializer Posts
```

this creates this:
```
<!-- app/serializers/post_serializer.rb -->
class PostSerializer < ActiveModel::Serializer
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
Add a nested association:
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

I struggled for a while with to_json, which is still good and fast for simple direct associations OR methods, but I never got it to work correctly with a association AND methods. It just didn't seem as flexible, and it may also may be depricated. Although, as of this writing, the active_record_serializers gem is still being developed, it's working swimmingly. Thumbs up! # Enter your title here

The content of your blog post goes here.
