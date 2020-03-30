#### Solved?

 No

#### Tried:

 Code:
 
 ``` 
  public function subscribe(Request $request, MailerInterface $mailer)
    {
      $msg = '';
      $email = filter_var($request->request->get('email', ''), FILTER_VALIDATE_EMAIL);
      if($email !== FALSE) {
        $name = substr($email, 0, strpos($email, '@'));

        $content = $this->get('twig')->createTemplate(
          "<p>Hello ${name}.</p><p>Thank you for subscribing to our newsletter.</p><p>Regards, VolgaCTF Team</p>"
        )->render();

        $mail = (new Email())->from('newsletter@newsletter.q.2020.volgactf.ru')->to($email)->subject('VolgaCTF Newsletter')->html($content);
        $mailer->send($mail);

        $msg = 'Success';
      } else {
        $msg = 'Invalid email';
      }
      return $this->render('main.twig', ['msg' => $msg]);
    }
 ```

The code is basically checking for the users email wheather it's a valid email or not and if yes then it'll subsitute the value before @ in the email address
as the user's name and try to render in the template with any sanitization.

As soon as we saw usage of Twig we had intuition of Template Injection in our heads but we had some obstacles to face.
1) FILTER_VALIDATE_EMAIL which would check for the validity of the email address.I stumbled upon a good StackOverflow thread where some
details were shed on what chars are permitted even after using the function and was mentioned that **within double quotes ["] almost every character
is allowed** which I just overlooked for no reason.

**Ref:- https://stackoverflow.com/questions/19220158/php-filter-validate-email-does-not-work-correctly**

2) Twig was using newer version and no such information was there on the Internet about it's exploitation and because I totally missed the usage of 
Double quotes I was stuck with not using [(] opening and closing brackets which made it hard for me.

I read Documentation and landed up on few filters but wasn't able to use them because of my **intuition about brackets being blocked.**

#### What are filters anyway?

```In some cases, as an editor, we are required to edit the value of any twig variable. Variables in Twig can be modified with help of twig filters. Filters are simply separated from variables by a pipe symbol ( | ). For applying twig filter we only need to apply the ( | ) followed by filter name. Twig comes with many filters built into it, and Drupal has a variety of filters native to it. We can chain multiple filters, the output of one filter is thus applied to the next.```

One such filter was `file_excerpt` (https://symfony.com/doc/current/reference/twig_reference.html#file-excerpt) which would just extract short amount of file content
which user define as an argument.

```{{ file|file_excerpt(line, srcContext = 3) }}```

As the flag was in the passwd file so our payload would have become ```payload@email.com``` -> ```"{{'/etc/passwd'|file_excerpt(1,30)}}"@domain.com``` which would handover our flag.


#### Solutions:

 - https://blog.blackfan.ru/2020/03/volgactf-2020-qualifier-writeup.html?m=1
