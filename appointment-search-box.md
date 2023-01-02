# Appointment Search Box (Screenshot)

![image](https://user-images.githubusercontent.com/77357735/210219999-39ae5e9c-1c20-4990-8b98-9ff3d3f737b8.png)

<!-- TABLE OF CONTENTS -->

## Table of Contents

- [Appointment search box UI](#appointment-search-box-ui)
- [After validation submits the search form and shows the result.](#open-modal-box)
- [After submitting the search form received the request in the controller and return the result as JSON.](#backend-handling-with-ruby)
- [Contact](#contact)

## Appointment search box UI

Appointment search box UI using HAML and Bootstrap.
````
.container-fluid{style: 'overflow-x: hidden'}
  .row
    .col-4.form-group
      = label_tag 'search_input', 'Suche nach Kunden, Kfz, VIN, oder Auftragsnummer'
      = text_field_tag 'search_input', nil,class:'form-control', title:'Suche nach Kunden, Auftragsnummer, Fahrgestellnummer oder Kennzeichen', value: @search_for
    .col-3.form-group
      = label_tag 'appointment_date_picker', 'Suche ab diesem Datum '
      = text_field_tag 'appointment_date_picker', nil, class:'dailySearchDate form-control w-75', title:'Datum auswählen',readonly:true
    .col-1.d-flex.align-items-center
      .new-fuzzy-search.btn.btn-primary.border.border-0{style: 'background: #ff9c38 !important; margin-top: 15px;'} Suchen
    .col-3.form-group
      %div
        = label_tag 'all_branch', 'Filialübergreifend suchen'
      %div
        = check_box_tag 'all_branch', "1", false
  .row
    .col-12.tableFixHead.result-container
      %table.table.table-striped.bg-light
        %thead
          %tr
            %th.col-1
              .d-flex.justify-content-between
                %div.tableSortName.nameColumn Kundenname
                %i.fa.fa-sort.mt-1.ml-1.searchSortIcon.nameColumnDefault.tableSortName
                %i.fa.fa-sort.mt-1.ml-1.searchSortIcon.nameColumn.tableSortName.d-none
            %th.col-1{scope:"col"}
              %div.d-flex.justify-content-between
                %div.tableSortDate.dateColumn Datum
                %i.fa.fa-sort.mt-1.ml-1.searchSortIcon.DateColumnDefault.tableSortDate
                %i.fa.fa-sort.mt-1.ml-1.searchSortIcon.DateColumn.tableSortDate.d-none
            %th.col-1{scope:"col"}
              %div.d-flex.justify-content-between
                %div.tableSortAbholDate.abholColumn Abholdatum
                %i.fa.fa-sort.mt-1.ml-1.searchSortIcon.AbholColumnDefault.tableSortAbholDate
                %i.fa.fa-sort.mt-1.ml-1.searchSortIcon.AbholColumn.tableSortAbholDate.d-none
            %th.col-1{scope:"col"} Kennzeichen
            %th.col-1{scope:"col"} VIN
            %th.col-2{scope:"col"} Serviceberater
            %th.col-3{scope:"col"} Eingriffe
            %th.col-2{scope:"col"}
              %div.d-flex
                %div.tableSortFiliale.branchColumn Filiale
                %i.fa.fa-sort.mt-1.ml-1.searchSortIcon.branchColumnDefault.tableSortFiliale
                %i.fa.fa-sort.mt-1.ml-1.searchSortIcon.branchColumn.tableSortFiliale.d-none
        %tbody.searchTableBody#search_table_body
          %tr.resultRowTemplate.d-none
            %td.sortedNameColumn.goToAppointment
              %div.resultCell #appointment_customer_name
            %td.sortedDataColumn
              .d-flex.justify-content-between
                %div.resultCell.sortedDataColumn.goToDay.pointer #appointment_date
                %i.fa.fa-trash.whyDeleted.displayNone
            %td.sortedAbholColumn
              %div.resultCell.sortedAbholColumn #pick_up_time
            %td #licence_nr
            %td #appointment_vin
            %td #appointment_serviceberater
            %td #appointment_interventions
            %td.sortedAddressColumn
              %div.resultCell.sortedAddressColumn #appointment_address
  %div{class:'noResult', id:'no_result'}

````

Modal box for deleted appointment.

````haml
  %div{class:'whyDeletedModalBox', style:'display:none; width:450px; height:250px; background-color: gray;'}
    %div{class:'imgLabelWhyDeleted'}
      %div{class:'whyDeletedImg', style: "background-image: url(#{image_path('papierkorb_icon.png')});"}
      %div{class:'whyDeletedLabel'} LÖSCHGRUND

    .dividerOrange-1-wtp
      %div{style:'padding: 10px;'}
        %div{class:'', style:'text-align: center;'}
          %div{class:'whyDeletedText div-text-dummy-1-wtp no-background-1-wtp', id:'why_deleted_text'}
          %br
          %textarea{id:'deleted_reason', class:'textarea-dummy-1-wtp', style:'width: 330px;'}

          %div{style:'margin-top: 20px;'}
          = button_bar(class: 'd-flex justify-content-center button-bottom') do
            = button_bar_button(position: "", text: "Schließen", class: "close-whyDeletedModalBox")
````
## Open Modal Box

Add functionality

````javaScript
$(document).ready(function () {


    var _new_data;

    $('#appointment_date_picker').datepicker({
        prevText: '&#x3c;zurück', prevStatus: '',
        prevJumpText: '&#x3c;&#x3c;', prevJumpStatus: '',
        nextText: 'Vor&#x3e;', nextStatus: '',
        nextJumpText: '&#x3e;&#x3e;', nextJumpStatus: '',
        currentText: 'heute', currentStatus: '',
        todayText: 'heute', todayStatus: '',
        clearText: '-', clearStatus: '',
        closeText: 'schließen', closeStatus: '',
        monthNames: ['01', '02', '03', '04', '05', '06',
            '07', '08', '09', '10', '11', '12'],
        monthNamesShort: ['Jan', 'Feb', 'Mär', 'Apr', 'Mai', 'Jun',
            'Jul', 'Aug', 'Sep', 'Okt', 'Nov', 'Dez'],
        dayNames: ['Sonntag', 'Montag', 'Dienstag', 'Mittwoch', 'Donnerstag', 'Freitag', 'Samstag'],
        dayNamesShort: ['So', 'Mo', 'Di', 'Mi', 'Do', 'Fr', 'Sa'],
        dayNamesMin: ['So', 'Mo', 'Di', 'Mi', 'Do', 'Fr', 'Sa'],
        showMonthAfterYear: false,
        showOn: 'both',
        buttonImage: 'media/img/calendar.png',
        buttonImageOnly: true,
        dateFormat: 'dd.MM.yy',
    }).keydown(function(e) {
        if(e.keyCode == 8 || e.keyCode == 46) {
            $.datepicker._clearDate(this);
            $("#search_input").focus();
        } else if (e.keyCode == 13) {
            e.preventDefault();
        }
    });

    var x = $('.ui-datepicker').addClass('appointmentDatePicker');

    $('.appointmentDatePicker').css('background', '#969696');

    $('.close-dialog').button().click(function () {
        $.modal.close({
            minHeight: 400,
            minWidth: 600
        });
    });

    $('.close-whyDeletedModalBox').click(function () {
        $.modal.close();
    });
    var _sort_name = 'asc';
    var _sort_date = 'asc';
    var _sort_abhol_date = 'asc';
    var _sort_ort = 'asc';
    var _sort_status = 'asc';

    $("#search_input").keypress(function (e) {
        if (e.which == 13) {
            $('.new-fuzzy-search').button().click();
        }
    });

    // this is added to select the text on click
    // when someone clicks inside the date text field, we select the whole text
    $('.dailySearchDate').click(function () {
        $(this).select();
    });

    // on key up from the date textfield, we check if a given value is a number (positive or negative)
    // they we find the date of today, add the given value to it
    // then set the new date format
    // and show the new date in the textfield

    $('.dailySearchDate').keyup(function (e) {
        var date_value = $('.dailySearchDate').val();

        if (e.which == 13) {
            if (!isNaN(date_value)) {
                var today = new Date();
                var next_date = new Date();
                next_date.setDate(today.getDate() + parseInt(date_value));
                var day = next_date.getDate();
                var month = next_date.getMonth() + 1;
                var year = next_date.getFullYear();
                var new_date_format = day + '.' + month + '.' + year;

                $('.dailySearchDate').val(new_date_format);
            }
        }
    });

    $('#all_branch').off().lc_switch('Ja', 'Nein');

    $('.new-fuzzy-search').button().click(function () {

        if ($('#search_input').val().trim() === '') {
            send_to_parent(error_notice(I18n.error.generic, 'Bitte geben Sie einen Suchbegriff ein'));
        } else {
            var search_input = $('#search_input').val();
            var _checkbox = $('#all_branch:checked').val();

            var date = $('.dailySearchDate').val();

            if (date < 0) {
                date = 0;

            }
            var _all_branch = 0;
            if (_checkbox) {
                _all_branch = 1;
            } else {
                _all_branch = 0;
            }

            var elem = $('.resultRow');
            elem.remove();
            $.ajax(
                {
                    url: '/daily_search/fuzzy_search/' + gon.branch_id + '?search_input=' + search_input + '&all_branch=' + _all_branch + '&uid=' + gon.user_id + '&date=' + date,
                    type: 'GET',
                    error: function () {
                    },
                    success: function (data) {
                        _new_data = data;
                        if (data.length < 1) {
                            $('.result-container').append('<div class = "resultRow alert alert-danger"">' + 'Für Ihren Suchbegriff wurde nichts gefunden' + '</div>');
                        }


                        main_function(_new_data, 0);
                    }

                }
            );
        }
    });
    // if the Kundenname header or its Arrows are clicked, the data will be sorted by customer name
    $('.tableSortName').click(function () {

        $('.searchSortIcon.DateColumnDefault').removeClass('d-none');
        $('.searchSortIcon.DateColumn').addClass('d-none');

        $('.searchSortIcon.branchColumnDefault').removeClass('d-none');
        $('.searchSortIcon.branchColumn').addClass('d-none');

        $('.searchSortIcon.AbholColumnDefault').removeClass('d-none');
        $('.searchSortIcon.AbholColumn').addClass('d-none');


        $('.searchSortIcon.nameColumnDefault').addClass('d-none');
        const changeable_icon_selector = $('.searchSortIcon.nameColumn');
        changeable_icon_selector.removeClass('d-none');
        var elem = $('.resultRow');
        elem.remove();
        if (_sort_name === "asc") {
            _new_data.sort(sort_by_asc('wp_kunde', false, function (a) {
                return a.toUpperCase()
            }));
            _sort_name = 'desc';

            if (changeable_icon_selector.hasClass('fa-sort')){
                changeable_icon_selector.removeClass('fa-sort');
                changeable_icon_selector.addClass('fa-sort-desc');
            }
            changeable_icon_selector.toggleClass('fa-sort-desc fa-sort-asc');

        } else if (_sort_name === "desc") {
            _new_data.sort(sort_by_desc('wp_kunde', false, function (a) {
                return a.toUpperCase()
            }));
            _sort_name = 'asc';
            changeable_icon_selector.toggleClass('fa-sort-desc fa-sort-asc');
        }
        main_function(_new_data, 1);

        $('.sortedCloumn').css('background-color', '#FFE6AF');

    });

    // if the Date header or its Arrows are clicked, the data will be sorted by date
    $('.tableSortDate').click(function () {


        $('.searchSortIcon.nameColumnDefault').removeClass('d-none');
        $('.searchSortIcon.nameColumn').addClass('d-none');

        $('.searchSortIcon.branchColumnDefault').removeClass('d-none');
        $('.searchSortIcon.branchColumn').addClass('d-none');

        $('.searchSortIcon.AbholColumnDefault').removeClass('d-none');
        $('.searchSortIcon.AbholColumn').addClass('d-none');

        $('.searchSortIcon.DateColumnDefault').addClass('d-none');
        const changeable_icon_selector = $('.searchSortIcon.DateColumn');
        changeable_icon_selector.removeClass('d-none');

        var elem = $('.resultRow');
        elem.remove();
        if (_sort_status === "asc") {
            _new_data.sort(sort_by_asc('sort_date', true, parseInt));
            _sort_status = 'desc';

            if (changeable_icon_selector.hasClass('fa-sort')){
                changeable_icon_selector.removeClass('fa-sort');
                changeable_icon_selector.addClass('fa-sort-desc');
            }
            changeable_icon_selector.toggleClass('fa-sort-desc fa-sort-asc');

        } else if (_sort_status === "desc") {
            _new_data.sort(sort_by_desc('sort_date', true, parseInt));
            _sort_status = 'asc';

            changeable_icon_selector.toggleClass('fa-sort-desc fa-sort-asc');

        }

        main_function(_new_data, 2);
        $('.sortedCloumn').css('background-color', '#FFE6AF');
    });

    // if the Pick up date header or its Arrows are clicked, the data will be sorted by pick up date
    $('.tableSortAbholDate').click(function () {

        $('.searchSortIcon.nameColumnDefault').removeClass('d-none');
        $('.searchSortIcon.nameColumn').addClass('d-none');

        $('.searchSortIcon.DateColumnDefault').removeClass('d-none');
        $('.searchSortIcon.DateColumn').addClass('d-none');

        $('.searchSortIcon.branchColumnDefault').removeClass('d-none');
        $('.searchSortIcon.branchColumn').addClass('d-none');

        $('.searchSortIcon.AbholColumnDefault').addClass('d-none');
        const changeable_icon_selector = $('.searchSortIcon.AbholColumn');
        changeable_icon_selector.removeClass('d-none');

        var elem = $('.resultRow');
        elem.remove();
        if (_sort_abhol_date === "asc") {
            _new_data.sort(sort_by_asc('sort_abhol_date', true, parseInt));
            _sort_abhol_date = 'desc';

            if (changeable_icon_selector.hasClass('fa-sort')){
                changeable_icon_selector.removeClass('fa-sort');
                changeable_icon_selector.addClass('fa-sort-desc');
            }
            changeable_icon_selector.toggleClass('fa-sort-desc fa-sort-asc');

        } else if (_sort_abhol_date === "desc") {
            _new_data.sort(sort_by_desc('sort_abhol_date', true, parseInt));
            _sort_abhol_date = 'asc';

            changeable_icon_selector.toggleClass('fa-sort-desc fa-sort-asc');

        }

        main_function(_new_data, 3);
        $('.sortedCloumn').css('background-color', '#FFE6AF');
    });

    // if the Address header or its Arrows are clicked, the data will be sorted by Address
    $('.tableSortFiliale').click(function () {

        $('.searchSortIcon.nameColumnDefault').removeClass('d-none');
        $('.searchSortIcon.nameColumn').addClass('d-none');

        $('.searchSortIcon.DateColumnDefault').removeClass('d-none');
        $('.searchSortIcon.DateColumn').addClass('d-none');

        $('.searchSortIcon.AbholColumnDefault').removeClass('d-none');
        $('.searchSortIcon.AbholColumn').addClass('d-none');

        $('.searchSortIcon.branchColumnDefault').addClass('d-none');

        const changeable_icon_selector = $('.searchSortIcon.branchColumn');
        changeable_icon_selector.removeClass('d-none');

        var elem = $('.resultRow');
        elem.remove();
        if (_sort_ort === "asc") {
            _new_data.sort(sort_by_asc('ort', false, function (a) {
                return a.toUpperCase()
            }));
            _sort_ort = 'desc';

            if (changeable_icon_selector.hasClass('fa-sort')){
                changeable_icon_selector.removeClass('fa-sort');
                changeable_icon_selector.addClass('fa-sort-desc');
            }
            changeable_icon_selector.toggleClass('fa-sort-desc fa-sort-asc');

        } else if (_sort_ort === "desc") {
            _new_data.sort(sort_by_desc('ort', false, function (a) {
                return a.toUpperCase()
            }));
            _sort_ort = 'asc';

            changeable_icon_selector.toggleClass('fa-sort-desc fa-sort-asc');

        }

        main_function(_new_data, 4);
        $('.sortedCloumn').css('background-color', '#FFE6AF');
    });

    $('#search_input').focus();
    parent.postMessage('notice_me_senpai', 'http://' + gon.hostname);

    if (typeof (gon.search_for) !== 'undefined' && gon.search_for != "") {
        $('.new-fuzzy-search').click();
    }
});


// a function to sort in Asc order
var sort_by_asc = function (field, reverse, primer) {
    var key = primer ?
        function (x) {
            return primer(x[field])
        } :
        function (x) {
            return x[field]
        };

    reverse = !reverse ? 1 : -1;

    return function (a, b) {
        return a = key(a), b = key(b), reverse * ((a > b) - (b > a));
    }
}

// a function to sort in desc order
var sort_by_desc = function (field, reverse, primer) {
    var key = primer ?
        function (x) {
            return primer(x[field])
        } :
        function (x) {
            return x[field]
        };

    reverse = !reverse ? 1 : -1;

    return function (a, b) {
        return a = key(a), b = key(b), reverse * ((a < b) - (b < a));
    }
}

// this function sends the user inside a specified Appointment

function go_to_termin(termin_id) {
    redirect_modal("modules.php?name=Werkstatt&op=AendernTermin&wp_term_id=" + termin_id);
}

// prepares the why_deleted modal box, which shows the time, date, and reason why an appointment is deleted
function why_deleted(termin_id) {
    $.ajax(
        {
            url: '/daily_search/why_deleted/' + gon.branch_id + '?termin_id=' + termin_id + '&uid=' + gon.user_id,
            type: 'GET',
            error: function () {

            },
            success: function (data) {

                var deleted_date = data.deleted_date;
                var deleted_time = data.deleted_time;
                var deleted_reason = data.deleted_reason;
                var deleted_by = data.deleted_by;

                var prepare_text = ' Durch ' + deleted_by +' am ' + deleted_date + ' um ' + deleted_time + ' Uhr eingegebener Löschgrund ';

                $('#why_deleted_text').text(prepare_text);
                $('#deleted_reason').text(deleted_reason);
                $(".whyDeletedModalBox").modal();


            }

        }
    )
}

// this function checkes if a user is allowed to update the appointment, based on this is the user allowed inside a specified Appointment
function runUpdate(termin_id, allow_update) {
    if (allow_update == 1) {

        go_to_termin(termin_id);
    }

}

// this function sends the user to the day of the appointment
function go_to_day(calender, hdl_konto, hdl_firma, hdl_filiale, appointment_id) {
    redirect_modal("modules.php?name=Werkstatt&op=wiedervorlage1tag&xlimit=&query=&xart=gleich&kaldat=" + calender + "&kontakte=alle&hdl_konto=" + hdl_konto + "&hdl_firma=" + hdl_firma + "&hdl_filiale=" + hdl_filiale + "&sortiermich=#einTag_anker_" + appointment_id);

}

// this function should check whether the appointment time is ( Nachtannahme, Terminpool, gelöscht or gesplitteter Termin)
function format_Appointment_time(time) {
    var _time = '';
    if (time == '03:31') {
        _time = 'Nachtannahme';
    } else if (time == '03:32') {
        _time = 'Terminpool';
    } else if (time == '21:00') {
        _time = '';
    } else if (time == '00:00') {
        _time = 'gesplitteter Termin';
    } else {
        _time = time;
    }
    return _time;
}

// this function should check whether the abhol_ time is (wartet or n. def.)
function format_Abholtime(time) {
    var abholtime = '';
    if (time == '00:00') {
        abholtime = 'wartet'
    } else if (time == '00:01') {
        abholtime = 'n. def.'
    } else {
        abholtime = time
    }
    return abholtime;
}

// this function should check whether the abhol date valid is
function format_abholdate(termin_date, abhol_date, _abhol_date) {
    var abholdate = '';
    var appointment = termin_date.replace(/(\.)/g, '');
    var abhol_appointment = termin_date.replace(/(\.)/g, '');

    if (abhol_appointment < appointment) {
        abholdate = '';
    } else if (abhol_date == 0) {
        abholdate = '';
    } else {
        abholdate = _abhol_date;
    }

    return abholdate;
}

// this is the main function which takes the data received either from the search or after being sorted and displays it accordingly
function main_function(_new_data, which_sort) {
    for (var i = 0; i < _new_data.length; i++) {


        var status = '';
        var abholdate = '';
        var abholtime = '';
        var _time = '';
        var why_deleted = false;
        var appointment_time = _new_data[i].wp_zeit;

        // this function should check whether the appointment time is ( Nachtannahme, Terminpool or gelöscht)
        _time = format_Appointment_time(_new_data[i].wp_zeit);

        // this function should check whether the abhol date valid is
        abholdate = format_abholdate(_new_data[i].sort_date, _new_data[i].sort_abhol_date, _new_data[i].wp_fertig_am);

        // this function should check whether the abhol_ time is (wartet or n. def.)
        abholtime = format_Abholtime(_new_data[i].wp_fertig_bis);

        if (_time == 'gelöscht') {
            status = "background-image: url(/assets/delete.gif)";
            why_deleted = true;
        }
        var appointment_date = _new_data[i].wp_datum + " " + _time;
        var pick_up_time = abholdate + " " + abholtime;
        var address = _new_data[i].ort + "\n\n" + _new_data[i].street;
        var customer_name = _new_data[i].wp_kunde;
        var licence_nr = _new_data[i].wp_kennz;
        var appointment_vin = _new_data[i].wp_fg_nr;
        var appointment_interventions = _new_data[i].interventions;
        var appointment_serviceberater = _new_data[i].wp_kd;
        var appointment_id = _new_data[i].wp_term_id;
        var allow_update = gon.allow_update;
        var allow_why_deleted = _new_data[i].allow_why_deleted;

        var _date = _new_data[i].wp_date;
        var _kontonr = _new_data[i].wp_kontonr;
        var _firma = _new_data[i].wp_firma;
        var _filiale = _new_data[i].wp_filiale;

        var x = $('.resultRowTemplate').clone(true).removeClass('resultRowTemplate d-none').addClass('resultRow').wrap("<div></div>").parent().html(function (i, html) {
            html = html.replace(/#appointment_customer_name/, customer_name);
            html = html.replace(/#appointment_date/, appointment_date);
            html = html.replace(/#pick_up_time/, pick_up_time);
            html = html.replace(/#licence_nr/, licence_nr);
            html = html.replace(/#appointment_vin/, appointment_vin);
            html = html.replace(/#appointment_interventions/, appointment_interventions);
            html = html.replace(/#appointment_serviceberater/, appointment_serviceberater);
            html = html.replace(/#appointment_address/, address);

            return html
        });

        var prepare_day = "go_to_day('" + _date + "','" + _kontonr + "','" + _firma + "','" + _filiale + "','" + appointment_id + "')";
        var prepare_appointment = "runUpdate('" + appointment_id + "','" + allow_update + "')";
        var prepare_why_deleted = "why_deleted('" + appointment_id + "')";
        x.find('.goToDay').attr('onclick', prepare_day);
        x.find('.goToAppointment').attr('onclick', prepare_appointment);

        // checking if the user is allowed to go inside the appointment to update it
        if (allow_update > 0) {
            x.find('.goToAppointment').addClass('pointer')
        }

        // checking if the appoitment is deleted
        if (appointment_time == '21:00') {
            x.find('.whyDeleted').removeClass('displayNone');
        }

        // checking if user is allowed to view why an appointment is deleted
        if (allow_why_deleted) {
            x.find('.whyDeleted').attr('onclick', prepare_why_deleted);
        }

        x = $(x.unwrap().html());
        x.show();
        $('#search_table_body').append(x);

        // switching the value which_sort
        // 0 being the data received directly from the search result ( not sorted )
        // 1 is sort by name
        // 2 is sort by date
        // 3 is sort by pick up date
        // 4 is sort by address
        switch (which_sort) {
            case 0:
                break;
            case 1:
                x.find('.sortedNameColumn').addClass('sortedCloumn');
                break;
            case 2:
                x.find('.sortedDataColumn').addClass('sortedCloumn');
                break;
            case 3:
                x.find('.sortedAbholColumn').addClass('sortedCloumn');
                break;
            case 4:
                x.find('.sortedAddressColumn').addClass('sortedCloumn');
                break;
        }
    }
}

````
<sup align="right"><a href="#table-of-contents">Go to top</a></sup>

## Backend Handling with Ruby

Default placeholder as Ruby Array

````ruby
class DailySearchController < ApplicationPureBootstrapController
  include UserRight::Appointment

  def index
    params.require(:hdl_id)
    params.require(:uid)

    allowed_branches = allowed_branches_by_uid(params[:uid])

    @allow_cross_search = allowed_branches[:allow_cross_search]
    allow_update = allowed_branches[:allow_update]
    @search_for = params[:search_for].nil? ? "" : params[:search_for]
    gon.push(
        {
            allow_update: allow_update,
            search_for: @search_for
        }
    )
  end


  def fuzzy_search
    params.require(:search_input)
    params.permit(:date)
    params.require(:hdl_id)
    params.require(:all_branch)
    params.require(:uid)
    hdl_id = params[:hdl_id]
    #the branch where the search is triggerd
    _branch = Branch.find_by(hdl_id: params[:hdl_id])

    _date = params[:date]
    # the date which the user wants to start on searching
    if params[:date].length > 0
      new_date = params[:date].to_time.strftime("%Y-%m-%d")
    else
      new_date = ''
    end
    # the array of the appointments to be displayed
    _termin_all = []

    # the input of the user
    _search_input = params[:search_input]

    # the ckeck box value, if search across Branches is triggerd
    _all_branch = params[:all_branch]

    #the query
    queries = []

    allowed_branches = allowed_branches_by_uid(params[:uid])


    if _all_branch === "1"
      allowed_branches[:enabled_searches].each do |branch|
        queries << "(wp_firma = #{branch[:firma]} AND wp_filiale = #{branch[:filiale]})"
      end
    else
      allowed_branches[:enabled_searches].each do |branch|
        if branch[:firma] == _branch.hdl_firma && branch[:filiale] == _branch.hdl_filiale
          queries << "(wp_firma = #{_branch.hdl_firma} AND wp_filiale = #{_branch.hdl_filiale})"
        end
      end
    end

    query = queries.join("OR")
    _lengththe = query.length
    if query.length > 0

      result = Appointment.where(query).where("wp_datum >= ? ", new_date)
                   .where("wp_kunde LIKE :query OR wp_auftragnr LIKE :query OR wp_fg_nr LIKE :query OR wp_kennz LIKE :query", query: "%#{_search_input}%").order(wp_datum: :desc).limit(200)
      branch = Branch.all
      branches = []
      branch.each do |b|
        branches[b.hdl_firma] ||= []
        branches[b.hdl_firma][b.hdl_filiale] = b
      end
      delete_reasons = Setting.where(wie_module: 'loeschgrund').all

      why_deleted = []

      delete_reasons.each do |reason|
          why_deleted[reason.wie_firma] ||= []
          why_deleted[reason.wie_firma][reason.wie_filiale] = reason
      end


      result.each do |termin|

        # to check to which branch a Termin belongs to
        which_branch = branches[termin.wp_firma][termin.wp_filiale]
        # to find the delete reasons for each Termin
        which_why_deleted = why_deleted[termin.wp_firma][termin.wp_filiale]

        appointment = {
            :wp_term_id => termin.wp_term_id,
            :wp_kunde => '',
            :wp_datum => 0,
            :wp_firma => 0,
            :wp_filiale => 0,
            :wp_kontonr => 0,
            :wp_date => 0,
            :wp_zeit => 0,
            :wp_fertig_am => 0,
            :wp_fertig_bis => 0,
            :wp_kennz => termin.wp_kennz,
            :wp_fg_nr => termin.wp_fg_nr,
            :interventions => "",
            :wp_kd => "",
            :wp_aktive => termin.wp_aktiv,
            :sort_date => 0,
            :sort_abhol_date => 0,
            :ort => '',
            :street => '',
            :allow_why_deleted => 0
        }

        # removing the whitespace in front of the name, to ease the sort by name
        customer_name = termin.wp_kunde
        customer_name.gsub!((/^[ \t]+/), "")
        appointment[:wp_kunde] = customer_name
        _interv = Intervention.where(termin_id: termin.wp_term_id).each do |i|
          appointment[:interventions] << i.description << '; '
        end
        appointment[:wp_kd] = termin.wp_kd
        appointment[:wp_firma] = termin.wp_firma
        appointment[:wp_filiale] = termin.wp_filiale
        appointment[:wp_date] = termin.wp_datum.strftime("%Y.%m.%d")
        appointment[:wp_kontonr] = which_branch.hdl_konto
        appointment[:ort] = which_branch.hdl_ort
        appointment[:street] = which_branch.hdl_str
        appointment[:allow_why_deleted] = which_why_deleted.wie_onoff
        #formating date
        if termin.wp_datum
          appointment[:wp_datum] = termin.wp_datum.strftime("%d.%m.%Y")
          appointment[:sort_date] = termin.wp_datum.strftime("%Y%m%d%H%M")
        end
        # formating time
        if termin.wp_zeit
          appointment[:wp_zeit] = termin.wp_zeit.strftime("%H:%M")

        end
        #formating time
        if termin.wp_fertig_bis
          appointment[:wp_fertig_bis] = termin.wp_fertig_bis.strftime("%H:%M")
        end
        #formating date
        if termin.wp_fertig_am
          appointment[:wp_fertig_am] = termin.wp_fertig_am.strftime("%d.%m.%Y")
          appointment[:sort_abhol_date] = termin.wp_fertig_am.strftime("%Y%m%d%H%M")
        end
        _termin_all.push(appointment)

      end
    end


    render json: _termin_all


  end

  def why_deleted
    params.require(:termin_id)

    # seach the termin id exists in the appointment delete reasons table
    why_delted = AppointmentDeleteReason
                     .select('appointment_delete_reason.created_at, appointment_delete_reason.reason, can_users.name')
                     .joins("LEFT OUTER JOIN can_users ON appointment_delete_reason.user_id = can_users.uid")
                     .find_by(appointment_id: params[:termin_id])

    #the stuff we want to view
    _why_deleted = {
        deleted_date: why_delted.created_at.strftime("%d.%m.%Y"),
        deleted_time: why_delted.created_at.to_time.strftime("%H:%M"),
        deleted_reason: why_delted.reason,
        deleted_by: why_delted.name
    }
    #sent back to JS
    render json: _why_deleted
  end

  def daily_search_params
    params.require(:daily_search).permit(:search_input)
  end

end
  ````





<sup align="right"><a href="#table-of-contents">Go to top</a></sup>

<!-- CONTACT -->

## Contact

Rahman Habib - [habib2030@web.de](mailto:habib2030@web.de)

LinkedIn Profile: [smhabibjr](https://www.linkedin.com/in/smhabibjr)

Youtube Channel: [HabibJr](https://www.youtube.com/c/HabibJr)

Facebook Profile: [Habib Jr](https://www.facebook.com/smhabibjr)

Website : [habibjr.com](https://www.habibjr.com)

<sup align="right"><a href="#table-of-contents">Go to top</a></sup>
