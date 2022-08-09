# Trademark Management System

![image](https://user-images.githubusercontent.com/77357735/183359092-e543def1-480e-44c0-962d-086ab46b95ba.png)

## Table of Contents

- [Project Description](#project-description)
- [Technologies and Programing Languages](#technologies-and-programming-languages)
- Frontend Design
  - [Modal Dialog from php](#modal-dialog-from-php)
  - [Frontend Design with HAML and Bootstrap](#frontend-design-with-haml-and-bootstrap)
- Backend
  - [Backend Handling with Ruby](#backend-handling-with-ruby)
- [Contact](#contact)


## Project Description

## Technologies and Programming Languages

Frontend: [HAML](https://haml.info/), [Bootstrap](https://getbootstrap.com/)

Backend: [Ruby](https://www.ruby-lang.org/de/), [PHP](https://www.php.net)

Framework: [Ruby on Rails](https://rubyonrails.org/)


## Modal Dialog from php


````php
<?php
/**
 * Created by PhpStorm.
 * User: Habib
 * Date: 25.10.2021
 * Time: 15:19
 */

chdir(dirname(__FILE__) . "/../../../");
include_once ("mainfile.php");
require_once "modules/Werkstatt/wterm15/modalDialog_class.php";

$url = "http://" . $_SERVER["HTTP_HOST"] . ":5050/brand_management";

$prefs = array(
    "title" => NULL,
    "body_height" => "700px",
);

$modal = new modalDialog(
    "Markenverwaltung",
    "/eins/html/images/1-wtp_icons_ci/checklisten_hersteller_verwaltung.png",
    NULL,
    $url,
    null,
    $prefs
);
$modal->create("html");
````


## Frontend Design with HAML and Bootstrap


````haml
`.container
  .container-fluid
    .row.marken-container.mb-3#marken_container{:style=>"height:640px;position:relative"}
      - unique_id = 1
      - @marken.each do |single_element|
        - value = single_element.active
        .col-lg-6.marken_rows{:style=>"width:50%"}
          = hidden_field_tag "marken_db_id[]", single_element.id , :class => "marken_db_id"
          = check_box_tag 'marken_value[]',value,value, :class => "markenCheckbox", :type => "checkbox", :id => "unique_id"
          = text_field_tag "marken_name[]", single_element.marke_lang, disabled:true, :class => "marken_name ml-2"
          - unique_id += 1

= button_bar(class: 'button_center  d-flex justify-content-center button-bottom') do
  = button_bar_button(position: "", text: "Speichern", class: "markenverwaltungSaveButtons", id: "btnSubmit")
````

## Backend Handling with Ruby

Mapping: config/routes.rb
 <!-- routing for markenverwaltung rails -->
````ruby
  resources :brand_management, only: [:index, :update]
  patch 'brand_management/update' => "brand_management#update"
````
Controller: app/controllers/brand_management.rb
````ruby
class BrandManagementController < ApplicationPureBootstrapController
  def index
    @marken = Brand.where('marke_lang <> ?', 'Intern').order(:marke_lang)
  end

  def update
    begin
      error_occurred = []
      # convert json text into an object
      marken_update = JSON.parse(params[:marken_update])
        # check object length
        if marken_update.length > 0
          # loop all parameters
          marken_update.each do |single_marken|
            # update the marken status according to marken id
            update_marken_status = Brand.find_by(id: single_marken['marken_id'].to_i).update(active: single_marken['marken_status'])
            unless update_marken_status
              error_occurred.push(1)
            end
          end
        else
          head :ok
        end

      if error_occurred.length > 0
        head :not_found
      else
        head :ok
      end

    rescue Exception => e
      logger.info "error to save marken status: #{e.to_s}"
      render status: 404, json: {code: 404, message: 'Method Not Found'}
    end
  end
end

````

## Contact

Rahman Habib - [habib2030@web.de](mailto:habib2030@web.de)

LinkedIn Profile: [smhabibjr](https://www.linkedin.com/in/smhabibjr)

Youtube Channel: [HabibJr](https://www.youtube.com/c/HabibJr)

Youtube Channel: [HabibJr](https://www.facebook.com/smhabibjr)
