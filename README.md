$.ajax({
                type: 'POST',
                dataType: 'text',
                headers: {"Authorization": localStorage.getItem('token')},
                beforeSend: function (xhr) {
                    xhr.setRequestHeader('X-IBM-Client-Id', '@System.Configuration.ConfigurationManager.AppSettings["X-IBM-Client-Id"]');
                },
                url: 'https://apigateway-dev.com/Authenticate',
                 data: {
                     UserID: $("#TxtUserID").val(),
                     Password: $("#TxtPassword").val()
                 },
                success: function (data) {
                    console.log('200 ok');
                    console.log('data : ' + data);
                    FailedAttempCount = 0;
                    sessionStorage.removeItem("FailedAttempCount");
                    sessionStorage.removeItem("countDownDate");
                    $('#TxtIsInvalidAttempt').val(false);
                    //$('form#loginForm').submit();
                },
                 error: function (jqXHR, textStatus, errorThrown) {
                    console.log(jqXHR.status);
                    console.log(textStatus);
                     console.log(errorThrown);
                     //$('#TxtIsInvalidAttempt').val(true);
                     //$('#TxtGetInvalidAttempt').val(1);
                     //$('form#loginForm').submit();
                     $.preloader.stop();

                     FailedAttempCount += 1;
                     console.log(FailedAttempCount);

                     sessionStorage.setItem("FailedAttempCount", FailedAttempCount);

                     document.getElementById("notif").innerHTML = "Invalid login attempt " + sessionStorage.getItem("FailedAttempCount") + " times";

                     //sessionStorage.FailedAttempCount = FailedAttempCount;


                     if (sessionStorage.getItem("FailedAttempCount") > MaxLoginAttempt) {
                         $("input").attr('disabled', 'disabled');
                         $("button").attr('disabled', 'disabled');

                         var twentyMinutesLater = new Date();
                         twentyMinutesLater.setMinutes(twentyMinutesLater.getMinutes() + LoginAttemptCooldownInMinutes);

                            //var countDownDate = new Date("Jan 5, 2022 15:37:25").getTime();

                         if (sessionStorage.getItem("countDownDate") == undefined || sessionStorage.getItem("countDownDate") == null) {
                             // Set the date we're counting down to
                             countDownDate = new Date(twentyMinutesLater).getTime();
                             sessionStorage.setItem("countDownDate", countDownDate);
                         }
                         else {
                             countDownDate = sessionStorage.getItem("countDownDate");
                         }

                         var x = setInterval(function () {

                             // Get today's date and time
                             var now = new Date().getTime();

                             // Find the distance between now and the count down date
                             var distance = countDownDate - now;

                             // Time calculations for days, hours, minutes and seconds
                             var days = Math.floor(distance / (1000 * 60 * 60 * 24));
                             var hours = Math.floor((distance % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
                             var minutes = Math.floor((distance % (1000 * 60 * 60)) / (1000 * 60));
                             var seconds = Math.floor((distance % (1000 * 60)) / 1000);

                             // Display the result in the element with id="demo"
                             document.getElementById("timer").innerHTML = "Please wait " + minutes + " minute " + seconds + " second before re-login.";

                             // If the count down is finished, write some text
                             if (distance < 0) {
                                 clearInterval(x);
                                 document.getElementById("timer").innerHTML = "";
                                 document.getElementById("notif").innerHTML = "";
                                 $("input").removeAttr('disabled');
                                 $("button").removeAttr('disabled');
                                 sessionStorage.removeItem("FailedAttempCount");
                                 sessionStorage.removeItem("countDownDate");
                             }
                         }, 1000);

                     }

                },
                    complete: function () {
                        $.preloader.stop();
                    }
             })
