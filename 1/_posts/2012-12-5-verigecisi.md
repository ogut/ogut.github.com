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

1 TABLO OLUŞTURMA
Veri geçiş metodu olan create_table kullanacağız.Tipik kullanımı:

      create_table :products do |t|
        t.string :name
      end
sütunlarla beraber ürün tablosunu oluşturuyor ve implicted sütun olarak isimlerndiriliyor. Objekt size sütunlar oluşturmak için bloklar vermiştir. Bunu yapmanın iki yolu vardır. İlk ve çok bilinen yol :

     create_table :products do |t|
        t.column :name, :string, :null => false
     end

İkinci yolu ise ‘sexy method’ isimlendirilen bazı gereksiz sütunları atlamak yolu. Yerine string, integer gibi metodlar kullanılır. Sonradan gelen parametreler aynı oluyor..

     create_table :products do |t|
        t.string :name, :null => false
     end
Varsayılan create_table başlanıç anahtar olan ‘id’ yi oluşturuyor. Siz başlangıç anahtarın ismini :primary_key option la değiştire bilirsiniz. Ama ilgili modeli update yapmayı unutmamalısınız. Eğer başlangıç anahtar istemiyorsan mesela (HABTM katılım tablosu için)  bu özelliği :id => false ile geçe bilirsin. Eğer database in spesifik özeliklerini geçmek istiyorsan o zaman SQL fragment yerleştire bilirsin  :options option.. Mesela :

    create_table :products, :options => "ENGINE=BLACKHOLE" do |t|
        t.string :name, :null => false
    end

SQLye ENGINE=BLACKHOLE ilave edilecek yeni tablo oluşturmak için (MySQL kullandığın zaman kullanılacak olan ENGINE=InnoDB dur) 

2 TABLO DEĞİŞTİRME
Tablo oluşturmanın en yakın akrabası tablo değiştirmektir. Ve tabloda değişiklik yapmak için kullanılıyor. Bu aynı tablo oluşturmakla aynı şekilde kullanılıyor.
 
    change_table :products do |t|
        t.remove :description, :name
        t.string :part_number
        t.index :part_number
        t.rename :upccode, :upc_code
    end
isim sütunlarını ve açıklamayı siliyor ve part_number dizgi sütunu oluşturuluyor. Ve onu index’e ilave ediyor. Sonda bu  UPCCODE sütun oluyor.

3 ÖZEL YARDIMCILAR
ACTİVE RECORD ortak kullanım için bazı kısayollar oluşturuyor. Bu mesela created_at ve updated_at sütunlarını birlikte oluşturmaya örnek ola bilir. Bu metod böyledir:

    create_table :products do |t|
        t.timestamps
    end

yeni ürün tablosu oluşturacaktır her iki sütunla beraber artı id sütunu dahil.. öyle ki ;

    change_table :products do |t|
        t.timestamps
    end

bu sütunları tabloya ilave ediyor. 
Başka bir yardımcı reference(belongs_to)dur. Bu okunabilirliğin en sade şeklidir.

    create_table :products do |t|
        t.references :category
    end
uygun bir şekilde category_id sütunu oluşturuyor. Not  modelin ismini geçiyorsunuz sütunun ismini değil. Active Record senin yerine the_id yi ilave ediyor. Eğer polimorf belongs_to varsa reference her iki sütunu ilave edecektir.

    create_table :products do |t|
        t.references :attachment, :polymorphic => {:default => 'Photo'}
    end

attechment_id sütununu ve attecment_type sütununu ilave ediyor PHOTO ile birlikte
Referanslar helper aslında sizin için yabancı anahtar kısıtlamaları yaratmaz. 
