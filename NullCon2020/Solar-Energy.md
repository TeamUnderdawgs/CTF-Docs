## Owner:

> CaptainFreak

#### Solved?

 - No

#### Tried:

 > No, less on hacker numbers, we are recruiting :)

#### Solutions:

The challenge was to exlpoit Apache Solr paramter injection vulnerability.
When the server makes S2S calls to solr API, and trusts user input to be included in the api queries, we can inject paramters if the input coming from user is not being URL encoded before sending it solr server.

Steps to solve the challenge:

1. Detect by injecting `({,},^,!,"` special chars.
2. Verify the inejection with urlencode(`*&fl=name,id&ident=true`).
3. Now we can use SSRF exploits from white-paper.
3. Use core-admins API to get core-names.
4. Use content type mismatch to Get `hackimsearch` and `SeCrEtSeArCh8888` core informations in errors as the frontend server talks only octet-stream and we can control what format gets outputed from Solr.
5. Get all docs of cores, get hint that we need to go looking into file system.
6. use `/core-name/file?wt=text` to  get filenames again using error based technique.
7. Now read the flag with exploit: `http://hackim/search=exploit&shards=http://localhost:8983/solr&qt=/SeCrEtSeArCh8888/admin/file?file=flag.txt&contentType=plain/text&wt=json` . Its url decoded for clearity.

In a nutshell:

![](https://www.draw.io/?lightbox=1&highlight=33FFFF&edit=_blank&layers=1&nav=1&title=Untitled%20Diagram.drawio#R1VZNb9swDP01OW5I7NRpj136ia1b1qBYt8ugxYytVhYNmW7s%2FfpRthzbdRu0QIF0J5tPpCQ%2BPgoc%2BfOkODcija8wBDXyxmEx8k9GnjeZHAb8sUhZI7MjB0RGhs6pBZbyLzhw7NBchpD1HAlRkUz74Aq1hhX1MGEMbvpua1T9U1MRwQBYroQaoj9kSHGNHnqzFr8AGcXNyZPgqF5JROPsMsliEeKmA%2FmnI39uEKn%2BS4o5KEtew0sdd%2FbM6vZiBjS9JCD8VS5nD9ffL39%2FvcqDOaWfv918mLq7UdkkDCHn70w0FGOEWqjTFv1kMNch2F3HbLU%2BXxBTBicM3gFR6YopckKGYkqUW4VC0m3n%2F6fd6uOBs04Kt3NllI2hyZS3XaMTZc02rLKauDo%2Fm9SztDkow9ysYAdXjfyEiYB2%2BHnb4nJXACbA9%2BE4A0qQfOjfQzh5Rlu%2FtoL844r4ioK6fR%2BEyt1JCyVKMNmg0G0ZbR02sSRYpqJiYMO93C%2BZ2xUMQbGbyGHiTUDgGsG9BN7M2Zu2ryZNs8Sdnmri3pyrg%2F9F%2FG8oYu%2BFIvb3KWJvIOILsbqXCWNnBjWBDvl3CYYFuXdd%2B95707U%2FoO%2BYCYjBcoaKGRsfLy73ztvB4XvjLRjwNvICRbbPOTaIqEq8RpRsEEfpQhhhBSr1Hc8hEnXjyVdpnZ8IX16fvdD1nDvWtmCWos5sNf9YOoGnHHsBXNdjEFn%2BucPL1LokMkvEivncdQSD3QwfCYNLTP3qZ2TwHuao%2BGD%2FRKO2L%2BNaKvUIEkpGmk0Fa7uDlYvk0erYwYkMw%2BpRfUps%2FYf2DfQ2nUz7evOHetuOnV29ea%2FXG5vtYFetdcZj%2F%2FQf)

References:
1. https://www.youtube.com/watch?v=xf2E64o4hWc
2. https://github.com/veracode-research/solr-injection#introduction
