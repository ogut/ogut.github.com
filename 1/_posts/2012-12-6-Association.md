---
layout: post
title:  ASSOCİATİONS
---

Neden modeller arasında etkileşime ihtiyaç duyarsınız ?
Çünkü onlar sayesinde kodlarımızda ki ortak işlemleri daha hızlı ve kolay hallederiz.
Örneğin müşteri ve siparişler için bir model içeren bir rails uygulamasını ele alalım.
Her müşterinin birden çok siparişi olabilir. Associtions olmadan şöyle olur 

    class Customer < ActiveRecord::Base
    end
 
    class Order < ActiveRecord::Base
    end

Şimdi de mevcut bir müşteri için yeni bir sipariş eklemek isteyelim.Bunun için de şöyle bişey yapmak gerekir.

    @order = Order.create(:order_date => Time.now,
        :customer_id => @customer.id)

Ya da bir müşteriyi silmek isteyelim aynı zaman da bu müşteriye ait bütün siaprişlerinde silinmesi gerekir :

    @orders = Order.where(:customer_id => @customer.id)
    @orders.each do |order|
        order.destroy
    end
      
    @customer.destroy


    class Customer < ActiveRecord::Base
      has_many :orders, :dependent => :destroy
    end

 
    class Order < ActiveRecord::Base
        belongs_to :customer
    end

Bu değişikliklerle birlikte müşteri için yeni bir düzen oluşturmak oldukça kolaydır :

    @order = @customer.orders.create(:order_date => Time.now)

Bir müşteri ve onun tüm siparişlerini silmek artık çok kolaydır :

    @customer.destroy



