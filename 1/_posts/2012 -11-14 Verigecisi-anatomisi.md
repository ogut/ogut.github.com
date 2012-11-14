---
layout: post
title: Veri Geçişinin Anatomisi
---

### Veri geçişlerini incelemeden önce burada bir kaç örnek inceleyelim 
class CreateProducts < ActiveRecord::Migration
  def up
    create_table :products do |t|
      t.string :name
      t.text :description
 
      t.timestamps
    end
  end
 
  def down
    drop_table :products
  end
end
 
 Bu migration adında ki tabloya sutun adı ve açiklama ekler.
 Id adında bir birincil anahtar sutun eklenecektir.
Kayıtlara otomatik olarak zaman damgasi sutunlari created_at ve updated_at aynı zamanda eklenecektir
	