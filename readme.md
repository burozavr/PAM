



      
  <div id="readme" class="Box-body readme blob js-code-block-container">
    <article class="markdown-body entry-content p-3 p-md-6" itemprop="text"><h2><a id="user-content-домашнее-задание" class="anchor" aria-hidden="true" href="#домашнее-задание"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Домашнее задание</h2>
<h4><a id="user-content-pam" class="anchor" aria-hidden="true" href="#pam"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>PAM</h4>
<ol>
<li>Запретить всем пользователям, кроме группы admin логин в выходные и праздничные дни</li>
<li>Дать конкретному пользователю права рута</li>
</ol>
<p>Необходимо решить задачу по ограничению доступа пользователей в систему  по  ssh.<br>
Это  будут  пользователи: "day", "night", "friday". Введем для них соответственно ограничения:</p>
<pre><code>●"day" - имеет удаленный доступ каждый день с 8 до 20;
●"night" - с 20 до 8;
●"friday" - в любое время, если сегодня пятница.
</code></pre>
<p>Создадим 3х пользователей:</p>
<pre><code>sudo useradd day &amp;&amp; \ sudo useradd night &amp;&amp; \sudo useradd friday
</code></pre>
<p>Назначим им пароли:</p>
<pre><code>echo "Otus2019"|sudo passwd --stdin day &amp;&amp;\echo "Otus2019" | sudo passwd --stdin night  &amp;&amp;\echo "Otus2019" | sudo passwd --stdin friday
</code></pre>
<p>Чтобы быть уверенными, что на стенде разрешен вход через ssh по паролю выполним:</p>
<pre><code>sed -i 's/^PasswordAuthentication.*$/PasswordAuthentication yes/' /etc/ssh/sshd_config &amp;&amp; systemctl restart sshd.servic
</code></pre>
<p>PAM (Pluggable Authentication Modules - подключаемые модули аутентификации)  -  это набор  библиотек,<br>
которые  позволяют интегрировать  различные  методы  аутентификации  в  виде  единого API,
что позволяет предоставить единые механизмы для управления, встраивания прикладных программ в процесс аутентификации.
PAM решает следующие задачи:</p>
<ul>
<li>Authentication - Аутентификация, идентификация, процесс подтверждения пользователем своей “подлинности”, ввод логина и пароля;</li>
<li>Authorization - Авторизация, процесс наделения пользователя правами (предоставления доступа к каким-либо объектам);</li>
<li>Accounting - Запись информации о произошедших событиях.</li>
</ul>
<p>Таким образом для решения задачи необходимо на первом или втором этапе применить необходимые нам проверки.
Их можно реализвать несколькими способами.
Рассмотрим их</p>
<h3><a id="user-content-pam-модуль-pam_time" class="anchor" aria-hidden="true" href="#pam-модуль-pam_time"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>PAM. Модуль pam_time</h3>
<p>Модуль pam_time позволяет достаточно гибко настроить доступ пользователя с учетом времени.
Настройки данного модуля хранятся в файле /etc/security/time.conf.
Данный файл содержит в себе пояснения и примеры использования.
Добавим в конец файла строки:</p>
<pre><code>*;*;day;Al0800-2000
*;*;night;!Al0800-2000
*;*;friday;Fr

</code></pre>
<p>Разные параметры отделяются символом ";". Разберем первую строку:
● “<em>” сервис, к которому применяется правило
● "</em>" имя терминала, к которому применяется правило
● имя пользователя, для которого данное правило будет действовать
● время, когда правило носит разрешающий характер</p>
<p>Теперь настроим PAM, так как по-умолчанию данный модуль не подключен.
Для этого приведем файл /etc/pam.d/sshd к виду:</p>
<pre><code>...
account    required     pam_nologin.so
account    required     pam_time.so
...
</code></pre>
<p>После чего в отдельном терминале можно проверить доступ к серверу по ssh для созданных пользователей.</p>
<p>PAM. Модуль pam_execЕще один способ реализовать задачу это выполнить при подключении
пользователя скрипт, в котором мы сами обработаем необходимую информацию.
Удалим из /etc/pam.d/sshd
изменения из предыдущего этапа и приведем его к следующему виду:</p>
<pre><code>...
account  required   pam_nologin.so
account  required   pam_exec.so  /usr/local/bin/test_login.sh
...
</code></pre>
<p>Мы добавили модуль pam_exec и, в качестве параметра, указали скрипт,
который осуществит необходимые проверки.</p>
<p>При  запуске  данного  скрипта  PAM-модулем  будет  передана переменная  окружения PAM_USER,
содержащая  имя  пользователя. Скрипт содержит простую логику.
Если имя пользователя friday, то проверям день недели, если пятница, то возвращаем 0,
если нет, то 1 и завершаем скрипт.Если же указан другой пользователь,
то в строке is_day_hours=$(($(test $hour -ge 8; echo $?)+$(test $hour -lt 20; echo $?)))происходит  проверка  принадлжит  ли  текущее  значение  времени (переменная   hour)  диапазону  от  8  до  20  часов.  Если  да,  то is_day_hours примет значение 0, если нет 1. Дальше проверяем имя пользователя  и  соотвествие  ему.  Если  пользователь day  и  часы "дневные",  то  возвращаем  0,  если  пользователь night  и  часы  НЕ дневные,  то  так  же  возвращаем  ноль.  В  противном  случае  скрипт вернет 1. Если в PAM_USER указано какое-то другое имя пользователя, то скрипт вернет 0.На основании кода завершения скрипта модуль pam_exec принимает решение. Если вернулся 0, то все в порядке и пользователь будет авторизован,
в обратном случае нет.
Данный модуль не входит, как предыдущие в базовую систему и должен быть установлен из отдельного репозитория Extra Packages for Enterprise Linux (EPEL).
Подключим репозиторий и установим pam_script:</p>
<pre><code>for pkg in epel-release pam_script; do yum install -y $pkg; done
</code></pre>
<p>Так же как и pam_exec модуль предназначен для выполнения произвольного скрипта в процессе авторизации, аутентификации или аккаунтинга пользователя. По сравнению с предыдущим примером в файле /etc/pam.d/sshdнужно просто переименовать pam_exec в pam_script:</p>
<pre><code>...
account  required  pam_nologin.so
account  required  pam_script.so  /usr/local/bin/test_login.sh
...
</code></pre>
<h3><a id="user-content-pam-модуль-pam_cap" class="anchor" aria-hidden="true" href="#pam-модуль-pam_cap"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>PAM. Модуль pam_cap</h3>
<p>Для демонстрации работы модуля установим дополнительный пакет nmap-ncat(CentOS).
Пользователь day остался из предыдущего примера.
Если стенд пересоздавался, то пользователя так же необходимо создать.
Войдем на стендовую машину под пользователем day и попробуем выполнить команду nc и
получим сообщение об ошибке.
Пример ниже.</p>
<pre><code>ncat -l -p 80Ncat: bind to :::80: Permission denied. QUITTING.
</code></pre>
<p>Это связано с тем, что непривелигерованный пользователь day,
от имени которого выполняется команда,
не может открыть для прослушивания 80й порт</p>
<p>Эту задачу можно решить несколькими способами:
● установить suid-бит. Установка данного бита позволит выполнить ncat так, будто он запущен от root. Способ имеет низкую гибкость, так как установка бита позволит любомупользователю выполнить команду;
● Предоставить пользователю права (возможности), чтобы он смог открыть порт. Способ более гибкий, потому что можно указать что именно, кому и при помощи какой программы мы разрешаем;</p>
<p>Решим задачу вторым способом. Для этого воспользумся pam-модулем pam_cap.
Поскольку это демо стенд, то SELinux можно просто выключить выполнив</p>
<pre><code>setenforce 0
</code></pre>
<h4><a id="user-content-отключать-selinux-в-продакшене-крайне-не-рекомендуется" class="anchor" aria-hidden="true" href="#отключать-selinux-в-продакшене-крайне-не-рекомендуется"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Отключать SELinux в продакшене крайне не рекомендуется!</h4>
<p>Приведем файл /etc/pam.d/sshd к виду:</p>
<pre><code>
...
auth       include      postlogin
auth       required     pam_cap.so
...
</code></pre>
<p>Таким  образом  мы  включили  обработку capabilities  при подключении  по ssh.<br>
Пропишем  необходимые  права  пользователю day.<br>
Для  этого  создадим  файл /etc/security/capability.conf содержащий одну стро</p>
<pre><code>cap_net_bind_service     day
</code></pre>
<p>Теперь необходимо программе (/usr/bin/ncat), при помощи которой будет  открываться  порт,<br>
так  же  выдать  разрешение  на  данное действие</p>
<pre><code> setcap cap_net_bind_service=ei /usr/bin/ncat
</code></pre>
<p>Мы  сопоставили  права,  выданные  пользователю  с  правами выданными на программу. Снова зайдем на стенд под пользователем day и проверим, что мы получили необходимые права:</p>
<h2><a id="user-content-права-администратора" class="anchor" aria-hidden="true" href="#права-администратора"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Права администратора</h2>
<p>Помимо внесения ограничений на вход пользователя в систему,
мы так  же  можем  предоставить  выбранному  пользователю  разные права.
Для  примера  рассмотрим  предоставление  прав  root'а определеному  пользователю  в  системе.<br>
Обычно  для  этого используются следующие варианты</p>
<p>● пользователь заносится в группу wheel;
● для него создается отдельный файл в /etc/sudoers.d/;
● отдельная строка в  /etc/sudoers.</p>
<p>Первый способ реализуется очень просто. Зайдя в систему под root'ом нужно выполнить:</p>
<pre><code>usermod -G wheel day
</code></pre>
<p>Теперь  зайдя  в  систему  под  пользователем day  можно  выполнить команду</p>
<pre><code>sudo  -i 
</code></pre>
<p>и получить консоль пользователя root. При этом будет  запрошен  пароль  того  пользователя,
под  которым осуществлен вход в систему (в данном случае day)</p>
<p>Последующие два способа сводятся к добавлению строки:</p>
<pre><code>day  ALL=(ALL)       ALL
</code></pre>
<p>или</p>
<pre><code>day        ALL=(ALL)       NOPASSWD: ALL
</code></pre>
<p>Первый  вариант  по-прежнему  будет  спрашивать  пароль  текущего пользователя,
а второй нет. Одну  из  приведенных  выше  строк  необходимо  добавить  к  файлу
/etc/sudoers  и  тогда  пользователь day  получит  возможность выполнить 'sudo'.</p>
<p>Вариант  с  созданием  файла /etc/sudoers.d/day  и  добавлением строки в него является более гибким и удобным.</p>
</article>
  </div>

    </div>

  

  <details class="details-reset details-overlay details-overlay-dark">
    <summary data-hotkey="l" aria-label="Jump to line"></summary>
    <details-dialog class="Box Box--overlay d-flex flex-column anim-fade-in fast linejump" aria-label="Jump to line">
      <!-- '"` --><!-- </textarea></xmp> --></option></form><form class="js-jump-to-line-form Box-body d-flex" action="" accept-charset="UTF-8" method="get">
        <input class="form-control flex-auto mr-3 linejump-input js-jump-to-line-field" type="text" placeholder="Jump to line&hellip;" aria-label="Jump to line" autofocus>
        <button type="submit" class="btn" data-close-dialog>Go</button>
</form>    </details-dialog>
  </details>

    <div class="Popover anim-scale-in js-tagsearch-popover"
     hidden
     data-tagsearch-url="/burozavr/Linux/find-symbols"
     data-tagsearch-ref="master"
     data-tagsearch-path="7_PAM/readme.md"
     data-tagsearch-lang="Markdown"
     data-hydro-click="{&quot;event_type&quot;:&quot;code_navigation.click_on_symbol&quot;,&quot;payload&quot;:{&quot;action&quot;:&quot;click_on_symbol&quot;,&quot;repository_id&quot;:244654780,&quot;ref&quot;:&quot;master&quot;,&quot;language&quot;:&quot;Markdown&quot;,&quot;originating_url&quot;:&quot;https://github.com/burozavr/Linux/blob/master/7_PAM/readme.md&quot;,&quot;user_id&quot;:58781965}}"
     data-hydro-click-hmac="54f6b162133ebfdbd4a5789f94b37efcc8bad1ae7a76f067fd82aedd68e33487">
  <div class="Popover-message Popover-message--large Popover-message--top-left TagsearchPopover mt-1 mb-4 mx-auto Box box-shadow-large">
    <div class="TagsearchPopover-content js-tagsearch-popover-content overflow-auto" style="will-change:transform;">
    </div>
  </div>
</div>



  </div>
</div>

    </main>
  </div>
  

  </div>

        
<div class="footer container-lg width-full p-responsive" role="contentinfo">
  <div class="position-relative d-flex flex-row-reverse flex-lg-row flex-wrap flex-lg-nowrap flex-justify-center flex-lg-justify-between pt-6 pb-2 mt-6 f6 text-gray border-top border-gray-light ">
    <ul class="list-style-none d-flex flex-wrap col-12 col-lg-5 flex-justify-center flex-lg-justify-between mb-2 mb-lg-0">
      <li class="mr-3 mr-lg-0">&copy; 2020 GitHub, Inc.</li>
        <li class="mr-3 mr-lg-0"><a data-ga-click="Footer, go to terms, text:terms" href="https://github.com/site/terms">Terms</a></li>
        <li class="mr-3 mr-lg-0"><a data-ga-click="Footer, go to privacy, text:privacy" href="https://github.com/site/privacy">Privacy</a></li>
        <li class="mr-3 mr-lg-0"><a data-ga-click="Footer, go to security, text:security" href="https://github.com/security">Security</a></li>
        <li class="mr-3 mr-lg-0"><a href="https://githubstatus.com/" data-ga-click="Footer, go to status, text:status">Status</a></li>
        <li><a data-ga-click="Footer, go to help, text:help" href="https://help.github.com">Help</a></li>

    </ul>

    <a aria-label="Homepage" title="GitHub" class="footer-octicon d-none d-lg-block mx-lg-4" href="https://github.com">
      <svg height="24" class="octicon octicon-mark-github" viewBox="0 0 16 16" version="1.1" width="24" aria-hidden="true"><path fill-rule="evenodd" d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.013 8.013 0 0016 8c0-4.42-3.58-8-8-8z"/></svg>
</a>
   <ul class="list-style-none d-flex flex-wrap col-12 col-lg-5 flex-justify-center flex-lg-justify-between mb-2 mb-lg-0">
        <li class="mr-3 mr-lg-0"><a data-ga-click="Footer, go to contact, text:contact" href="https://github.com/contact">Contact GitHub</a></li>
        <li class="mr-3 mr-lg-0"><a href="https://github.com/pricing" data-ga-click="Footer, go to Pricing, text:Pricing">Pricing</a></li>
      <li class="mr-3 mr-lg-0"><a href="https://developer.github.com" data-ga-click="Footer, go to api, text:api">API</a></li>
      <li class="mr-3 mr-lg-0"><a href="https://training.github.com" data-ga-click="Footer, go to training, text:training">Training</a></li>
        <li class="mr-3 mr-lg-0"><a href="https://github.blog" data-ga-click="Footer, go to blog, text:blog">Blog</a></li>
        <li><a data-ga-click="Footer, go to about, text:about" href="https://github.com/about">About</a></li>
    </ul>
  </div>
  <div class="d-flex flex-justify-center pb-6">
    <span class="f6 text-gray-light"></span>
  </div>
</div>



  <div id="ajax-error-message" class="ajax-error-message flash flash-error">
    <svg class="octicon octicon-alert" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M8.893 1.5c-.183-.31-.52-.5-.887-.5s-.703.19-.886.5L.138 13.499a.98.98 0 000 1.001c.193.31.53.501.886.501h13.964c.367 0 .704-.19.877-.5a1.03 1.03 0 00.01-1.002L8.893 1.5zm.133 11.497H6.987v-2.003h2.039v2.003zm0-3.004H6.987V5.987h2.039v4.006z"/></svg>
    <button type="button" class="flash-close js-ajax-error-dismiss" aria-label="Dismiss error">
      <svg class="octicon octicon-x" viewBox="0 0 12 16" version="1.1" width="12" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.48 8l3.75 3.75-1.48 1.48L6 9.48l-3.75 3.75-1.48-1.48L4.52 8 .77 4.25l1.48-1.48L6 6.52l3.75-3.75 1.48 1.48L7.48 8z"/></svg>
    </button>
    You can’t perform that action at this time.
  </div>


    <script crossorigin="anonymous" async="async" integrity="sha512-o4vS4IKrjdy/HD+xr2+VhO6DxQmj5jikhHbEGrd8+JGhpmIOxRrpT1Qo5k3IhKimm8VXIu3pyYejLtOAkm+OsQ==" type="application/javascript" id="js-conditional-compat" data-src="https://github.githubassets.com/assets/compat-bootstrap-a38bd2e0.js"></script>
    <script crossorigin="anonymous" integrity="sha512-2GtXiukHeT1/Kt5UHrVa2iMiBF1fCLQILWG0UKazKtQXjLZpcurZ6AXlkiTZFUeEtVWjoV8LvyppgPp9rkQMUA==" type="application/javascript" src="https://github.githubassets.com/assets/environment-bootstrap-d86b578a.js"></script>
    <script crossorigin="anonymous" async="async" integrity="sha512-b/eiTgUmQXvFSyXcioklOO+SOVe85tsZw2OyDiixI8/rzI71a+4eh2LljU/7co1ItCsS9iSI+wp+2BB0SMfK8A==" type="application/javascript" src="https://github.githubassets.com/assets/vendor-6ff7a24e.js"></script>
    <script crossorigin="anonymous" async="async" integrity="sha512-NfTVyRxwBPtNBYufyzMju4nWjLIjZ08N1+TmHaG4yUaLsT32mQ03TvQdbdyCCsqpsOfrnqk3IPdJfM59nKILHQ==" type="application/javascript" src="https://github.githubassets.com/assets/frameworks-35f4d5c9.js"></script>
    
    <script crossorigin="anonymous" async="async" integrity="sha512-aOs1h4Us1HJ9XUfKiaingGilqVOC9CG/XlKC7rG+ER0ywya6R+mbPUdz6cQ9rarweTxwOFqUz30yvCj7A/qSfA==" type="application/javascript" src="https://github.githubassets.com/assets/github-bootstrap-68eb3587.js"></script>
    
    
    
  <div class="js-stale-session-flash flash flash-warn flash-banner" hidden
    >
    <svg class="octicon octicon-alert" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M8.893 1.5c-.183-.31-.52-.5-.887-.5s-.703.19-.886.5L.138 13.499a.98.98 0 000 1.001c.193.31.53.501.886.501h13.964c.367 0 .704-.19.877-.5a1.03 1.03 0 00.01-1.002L8.893 1.5zm.133 11.497H6.987v-2.003h2.039v2.003zm0-3.004H6.987V5.987h2.039v4.006z"/></svg>
    <span class="js-stale-session-flash-signed-in" hidden>You signed in with another tab or window. <a href="">Reload</a> to refresh your session.</span>
    <span class="js-stale-session-flash-signed-out" hidden>You signed out in another tab or window. <a href="">Reload</a> to refresh your session.</span>
  </div>
  <template id="site-details-dialog">
  <details class="details-reset details-overlay details-overlay-dark lh-default text-gray-dark hx_rsm" open>
    <summary role="button" aria-label="Close dialog"></summary>
    <details-dialog class="Box Box--overlay d-flex flex-column anim-fade-in fast hx_rsm-dialog hx_rsm-modal">
      <button class="Box-btn-octicon m-0 btn-octicon position-absolute right-0 top-0" type="button" aria-label="Close dialog" data-close-dialog>
        <svg class="octicon octicon-x" viewBox="0 0 12 16" version="1.1" width="12" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.48 8l3.75 3.75-1.48 1.48L6 9.48l-3.75 3.75-1.48-1.48L4.52 8 .77 4.25l1.48-1.48L6 6.52l3.75-3.75 1.48 1.48L7.48 8z"/></svg>
      </button>
      <div class="octocat-spinner my-6 js-details-dialog-spinner"></div>
    </details-dialog>
  </details>
</template>

  <div class="Popover js-hovercard-content position-absolute" style="display: none; outline: none;" tabindex="0">
  <div class="Popover-message Popover-message--bottom-left Popover-message--large Box box-shadow-large" style="width:360px;">
  </div>
</div>

  <div aria-live="polite" class="js-global-screen-reader-notice sr-only"></div>

  </body>
</html>

