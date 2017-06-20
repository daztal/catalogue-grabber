# Программа на вход получает 1) ссылку (ссылка на страницу категории) 2) имя файла в который будет записан результат
# https://www.petsonic.com/snacks-huesos-para-perros/
require 'rubygems'
require 'nokogiri'
require 'open-uri'
require 'csv'

puts "Enter url of page with goods:" # задаем ссылку на страницу категории
pageURL = gets.strip!.to_s

puts "Enter filename you prefer:" # задаем имя файла в который будет записан результат
fileName = gets.strip!.to_s

if File.exists?(fileName + ".csv") # если файл с таким именем сушествует, то удалим его
  File.delete(fileName + ".csv")
end
f = File.new(fileName + "." + "csv", "a+") # и создадим новый

CSV.open(fileName + ".csv", "ab") do |hdr| # заголовки для столбцов
  hdr << ["Product name", "Product quantity", "Product price", "Product image"]
end


page = Nokogiri::HTML(open(pageURL))
lastPageNumber = page.css("ul.pagination.pull-left li")[-2].text.strip! # получаем номер последней страницы

pageNumber = 1

puts "Loading " + lastPageNumber.to_s + " pages, please wait..."

while pageNumber <= lastPageNumber.to_i # парсим постранично

  page = Nokogiri::HTML(open(pageURL + "?p=" + pageNumber.to_s))

  productLinks = page.css("a.product_img_link") # получаем ссылки на товары
  product_links = Array.new

  productLinks.each { |link|
      product_links.push(link['href'])} # складываем ссылки с массив

  product_links.each {|link|
    page = Nokogiri::HTML(open(link)) # открываем каждую ссылку в массиве
    productName = page.css("h1").text # берем из открывшейся страницы название товара
    cleanProductNames = productName.split(/\n/) # получаем массив
    cleanProductNames.each do |item| # убираем лишние пробелы
      item.strip!
    end
    cleanProductNames.delete("") # убираем пустые елементы массива

    productInf = page.css("ul.attribute_labels_lists").text # аналогично с весом и ценой
    cleanProductInf = productInf.split(/\n/)
    cleanProductInf.each do |item|
      item.strip!
    end
    cleanProductInf.delete("")

    productImages = page.css("img#bigpic").map{|i| i['src']} # аналогично с изображением

    counter = 0
    cleanProductInf.each do |item| # смотрим сколько раз в массиве есть символ евро, чтоб узнать сколько цен указано
      if item.include?("€") # делается чтобы вывести актуальную цену у товаров которые указаны со скидкой
        counter += 1
      end
    end

    CSV.open(fileName + ".csv", "ab") do |csv| # открываем таблицу и пишем

      if counter == 0
        csv << [cleanProductNames[1], "", "", productImages[0]]
      elsif counter == cleanProductInf.size / 2
        i = 0
        while i < cleanProductInf.size - 1
          csv << [cleanProductNames[1], cleanProductInf[i], cleanProductInf[i + 1], productImages[0]]
          i += 2
        end
      elsif counter > (cleanProductInf.size / 2)
        i = 0
        while i < cleanProductInf.size - 2
          csv << [cleanProductNames[1], cleanProductInf[i], cleanProductInf[i + 2], productImages[0]]
          i += 3
        end
      end

    end
  }
  puts "Loaded page № " + pageNumber.to_s
  pageNumber += 1
end
puts "CSV file was created"