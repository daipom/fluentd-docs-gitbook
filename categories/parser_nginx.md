<hgroup>
<h1>nginx Parser Plugin</h1>
</hgroup>
<p>The <code>nginx</code> parser plugin parses default nginx logs.</p>
<a name="parameters"></a>
<section id="table-of-contents"><h3>Table of Contents</h3>
<ul id="toc">
<li class="toc-item"><a href="#parameters">Parameters</a></li>
<ul class="sub-toc">
<li class="sub-toc-item"><a href="#keep_time_key">keep_time_key</a></li>
<li class="sub-toc-item"><a href="#types">types</a></li>
</ul>
<li class="toc-item"><a href="#regexp-patterns">Regexp patterns</a></li>
<li class="toc-item"><a href="#example">Example</a></li>
</ul>
</section>
<h2>Parameters</h2>
<a name="keep_time_key"></a><h3>keep_time_key</h3>
<p>If you want to keep time field in the record, set <code>true</code>. Default is <code>false</code>.</p>
<a name="types"></a><h3>types</h3>
<p>Although every parsed field has type <code>string</code> by default, you can specify other types. This is useful when filtering particular fields numerically or storing data with sensible type information.</p>
<p>The syntax is</p>
<pre class="CodeRay">types &lt;field_name_1&gt;:&lt;type_name_1&gt;,&lt;field_name_2&gt;:&lt;type_name_2&gt;,...
</pre>
<p>e.g.,</p>
<pre class="CodeRay">types user_id:integer,paid:bool,paid_usd_amount:float
</pre>
<p>As demonstrated above, “,” is used to delimit field-type pairs while “:” is used to separate a field name with its intended type.</p>
<p>Unspecified fields are parsed at the default string type.</p>
<p>The list of supported types are shown below:</p>
<ul>
<li>string</li>
<li>bool</li>
<li>integer (“int” would NOT work!)</li>
<li>float</li>
<li>time</li>
<li>array</li>
</ul>
<p>For the <code>time</code> and <code>array</code> types, there is an optional third field after the type name. For the “time” type, you can specify a time format like you would in <code>time_format</code>.</p>
<p>For the “array” type, the third field specifies the delimiter (the default is “,”). For example, if a field called “item_ids” contains the value “3,4,5”, <code>types item_ids:array</code> parses it as [“3”, “4”, “5”]. Alternatively, if the value is “Adam|Alice|Bob”, <code>types item_ids:array:|</code> parses it as [“Adam”, “Alice”, “Bob”].</p>
<a name="regexp-patterns"></a><h2>Regexp patterns</h2>
<p>This is regexp and time format patterns of this plugin:</p>
<pre class="CodeRay">format /^(?&lt;remote&gt;[^ ]*) (?&lt;host&gt;[^ ]*) (?&lt;user&gt;[^ ]*) \[(?&lt;time&gt;[^\]]*)\] "(?&lt;method&gt;\S+)(?: +(?&lt;path&gt;[^\"]*) +\S*)?" (?&lt;code&gt;[^ ]*) (?&lt;size&gt;[^ ]*)(?: "(?&lt;referer&gt;[^\"]*)" "(?&lt;agent&gt;[^\"]*)")?$/
time_format %d/%b/%Y:%H:%M:%S %z
</pre>
<p><code>remote</code>, <code>user</code>, <code>method</code>, <code>path</code>, <code>code</code>, <code>size</code>, <code>referer</code> and <code>agent</code> are included in the event record. <code>time</code> is used for the event time.</p>
<a name="example"></a><h2>Example</h2>
<pre class="CodeRay">127.0.0.1 192.168.0.1 - [28/Feb/2013:12:00:00 +0900] "GET / HTTP/1.1" 200 777 "-" "Opera/12.0"
</pre>
<p>This incoming event is parsed as:</p>
<pre class="CodeRay">time:
1362020400 (28/Feb/2013:12:00:00 +0900)

record:
{
  "remote" : "127.0.0.1",
  "host"   : "192.168.0.1",
  "user"   : "-",
  "method" : "GET",
  "path"   : "/",
  "code"   : "200",
  "size"   : "777",
  "referer": "-",
  "agent"  : "Opera/12.0"
}
</pre>
<div style="text-align:right">
  Last updated: 2018-10-19 00:25:54 +0000
  </div>
<hr size="1" style="margin-top: 10px; margin-bottom: 10px; color: rgba(0, 0, 0, .15);"/>
<div style="text-align:right">
Versions 
  
    
    | <a href="/v1.0/articles/parser_nginx">v1.0 (td-agent3)</a>
    
  

  

  
    
    | <b><i>v0.12</i> (td-agent2)<b>
</b></b>
</div>
<hr size="1" style="margin-top: 10px; margin-bottom: 10px; color: rgba(0, 0, 0, .15);"/>
<p>
    If this article is incorrect or outdated, or omits critical information, please <a href="https://github.com/fluent/fluentd-docs/issues?state=open">let us know</a>. <a href="http://www.fluentd.org/">Fluentd</a> is a  open source project under <a href="https://cncf.io/">Cloud Native Computing Foundation (CNCF)</a>. All components are available under the Apache 2 License.
  </p>