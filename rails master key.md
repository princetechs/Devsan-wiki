when we run rails credential:edit
it create a decrept key as master key inside master.key
and create a encrepetd file with secret_key_base
secret_key_base is require for our app cokkies and etc
using the master key rails can see the secret key and files


the write masterkey i have was :-
68ebb73ece0ae3c935741db67efbdbff
when i change it to 
68ebb73ece0ae3c935741db67efbdbfa
and run "rails credentials:show"
get :-
Users/sandipparida/.local/share/mise/installs/ruby/3.3.0/lib/ruby/gems/3.3.0/gems/activesupport-7.2.2.1/lib/active_support/messages/codec.rb:57:in `catch_and_raise': ActiveSupport::MessageEncryptor::InvalidMessage
        from /Users/sandipparida/.local/share/mise/installs/ruby/3.3.0/lib/ruby/gems/3.3.0/gems/activesupport-7.2.2.1/lib/active_support/message_encryptor.rb:242:in `decrypt_and_verify'
        from /Users/sandipparida/.local/share/mise/installs/ruby/3.3.0/lib/ruby/gems/3.3.0/gems/activesupport-7.2.2.1/lib/active_support/encrypted_file.rb:109:in `decrypt'
        from /Users/sandipparida/.local/share/mise/i

