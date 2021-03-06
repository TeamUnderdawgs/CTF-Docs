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

![](https://i.ibb.co/9cwzky1/Untitled-Diagram.png)

References:
1. https://www.youtube.com/watch?v=xf2E64o4hWc
2. https://github.com/veracode-research/solr-injection#introduction
