---
layout: post
title: Bağımsız Veri Geçişi  Oluşturma
---

Eğer veri geçişlerini farklı amaçlar için tasarlayacaksanız
mesela var olan tabloya sütun eklemek gibi migration generatorı kullanabilirsiniz.
    $ rails generate migration AddPartNumberToProducts

Bu boş ancak uygun bir şekilde isimlendirilmiş veri oluşturacak:
    class AddPartNumberToProducts < ActiveRecord::Migration
      def change
        end
    end

Eğer verinin ismi “AddXXXToYYY”  ve ya  “RemoveXXXFromYYY”  şeklindeyse sütun isimlerini ve türlerini listeliyor. Sonraysa add_column ve remove_coloumn oluşturuluyor.

    $ rails generate migration AddPartNumberToProducts part_number:string

Oluşacak :


    class AddPartNumberToProducts < ActiveRecord::Migration
      def change
        add_column :products, :part_number, :string
       end
     end

Benzer şekilde:

    $ rails generate migration RemovePartNumberFromProducts part_number:string

Oluşturuluyor :

    class RemovePartNumberFromProducts < ActiveRecord::Migration
      def up
        remove_column :products, :part_number
        end
      def down
        add_column :products, :part_number, :string
      end
    end

Kendinizi sadece bir sutun oluşturmakla sınırlamayabilirsiniz mesela :
    class AddDetailsToProducts < ActiveRecord::Migration
      def change
        add_column :products, :part_number, :string
        add_column :products, :price, :decimal
       end
    end

Her zaman sizin için oluşturulaN şey bir başlangoç noktasıdır. 
Ona bir şeyler ekleyebilir ve ya ondan bir şeyler  sile bilirsiniz.
Mesela gördüğünüz gibi yararlı düzenleme ile  db/migrate/YYYYMMDDHHMMSS_add_details_to_products.rb file.
İlave olunmuştur. Yıkıcı göçler için oluşturulan göç dosya hala yukarı ve aşağı
 yöntemleri kullanarak eski tarz olacak. Bunun sebebi railslerde değişikler 
yapıldığı zaman verilerin orijinal halini bilmesi gerekmesindendir.
