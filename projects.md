---
title: Projects
layout: default
---

<section class="container">
  <div class="row">
    <div class="text-center title">The DoD OSS Directory</div>
  </div>
  <div class="row">
    <div class="col-sm-6 col-sm-offset-2">
      <h4>Code.mil supports <strong>free and open source software development</strong> at the U.S. Department of Defense (DoD). The objective is to produce <strong>better software</strong> for the DoD, the nation, and the world, through <strong> community software development </strong>.</h4>
    </div>
    <div class="col-sm-2">
      <img class="img-responsive" src="{% if jekyll.environment == 'staging' %}{% else %}{{ site.baseurl }}{% endif %}{% link _assets/birdplaceholder.png %}" alt="DDS.mil" style="transform: scaleX(-1);" />
    </div>
  </div>
  <div class="row">
    <div class="text-center">
      <button class="col-sm-2 col-sm-offset-5 btn btn-default btn-lg">Submit a Repo</button>
    </div>
  </div>
  <div class="row">
    <div class="col-sm-3">
      <div class="dropdown">
        <button class="btn btn-default dropdown-toggle" type="button" id="dropdownMenu1" data-toggle="dropdown" aria-haspopup="true" aria-expanded="true">
          Dropdown
          <span class="caret"></span>
        </button>
        <ul class="dropdown-menu" aria-labelledby="dropdownMenu1">
          <li><a href="#">All Groups</a></li>
          <li><a href="#">Another action</a></li>
          <li><a href="#">Something else here</a></li>
        </ul>
      </div>
    </div>
    <div class="col-sm-3 col-sm-offset-6">
      <div class="input-group">
        <input type="text" class="form-control" placeholder="Search">
        <span class="input-group-btn">
          <button class="btn" type="button"><span class="glyphicon glyphicon-search"> </span></button>
        </span>
      </div>
    </div>
  </div>
</section>

<section class="row" id="repos">
<script>
// TODO: Will not scale, need Jenkins or AWS Lambda to update code.json
function addFields(p,response){
  var div = document.createElement('div');
  div.classList.add('panel-body');
  p.appendChild(div);
  var [a,b] = response.full_name.split("/");
  var tmp = document.createElement('div');
  tmp.innerText = a;
  div.appendChild(tmp);
  tmp = document.createElement('h2');
  tmp.innerText = b;
  div.appendChild(tmp);
  var array = [response.description
            ,"Last Updated: " + response.pushed_at.slice(0,9)
            ,response.language];
  for (var i=0;i<array.length;i++){
    tmp = document.createElement('p');
    tmp.innerText=array[i];
    div.appendChild(tmp);
  }
}
</script>
<script>
// Cache repo info in localStorage
function updateRepo(repo){
  var cache = localStorage.getItem(repo + ".date");
  var p = document.currentScript.parentNode;
  // 15 min invalidation
  if (Date.now() - Number.parseInt(cache) < 900 * 1000) {
    return addFields(p,JSON.parse(localStorage.getItem(repo)));
  }
  var xhr = new XMLHttpRequest();
  xhr.responseType = 'json';
  xhr.open('GET', 'https://api.github.com/repos/' + repo);
  xhr.onload = function() {
    if (xhr.readyState !== 4 ) { return }
    var response;
    if (xhr.status == 403) {
      console.log('Rate limit reached. Using localStorage');
      response = JSON.parse(localStorage.getItem(repo));
      if (!response) response = {full_name:repo,description : '',pushed_at:'',language:''};
    }
    else if (xhr.status !== 200 && xhr.status !== 304) {
      console.log(xhr.status);
      response = {full_name:repo,description : '',pushed_at:'',language:''};
    }
    else {response = xhr.response;}
    addFields(p,response);
    // Cache response
    localStorage.setItem(repo,JSON.stringify(response));
    localStorage.setItem(repo + ".date",Date.now());
  };
  xhr.send();
};

value = {{ site.data.projects.GitHubIndividualProjects | jsonify }};
</script>
{% for repo in site.data.projects.GitHubIndividualProjects %}
    <div class="col-sm-6">
        <a href="https://github.com/{{repo}}" class="col-sm-12 panel css3-shadow">
            <script>updateRepo("{{repo}}");</script>
        </a>
    </div>
{% endfor %}
</section>