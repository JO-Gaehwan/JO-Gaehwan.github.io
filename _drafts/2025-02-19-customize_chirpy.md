how to customize github chirpy theme blog

많은 부분은 

https://jason9288.github.io/posts/github_blog_4/

이 블로그의 시리즈를 보면 된다 몇가지 추가적인 사항을 이 페이지에 적어둔다

sidebar나 profile등의 색깔을 바꿀때에는 
_sass/themes/_dark.scss
에서 바꾸면 된다.

특히 sidebar에 대해서는

  /* Sidebar */
  --site-title-color: #cbc6c6;
  --site-subtitle-color: #cbc6c6;
  --sidebar-bg: #1e1e1e;
  --sidebar-border-color: #000000;
  --sidebar-muted-color: #ffffff;
  --sidebar-active-color: rgba(217, 187, 187, 0.95);
  --sidebar-hover-bg: #363636;
  --sidebar-btn-bg: #232328;
  --sidebar-btn-color: #ffffff;
  --avatar-border-color: rgba(15, 14, 14, 0.9);

  이부분을 건드려 주면 된다.


  sidebar의 글자 등의 위치 조절은

_sass/layout/_sidebar.scss

에서 조절 가능하다.

---
giscus로 comment feature 작업하였다.
config와 giscus.html을 변경해서 작업완료하였다.

---
google analytics등록함
참고: https://jason9288.github.io/posts/github_blog_4/

---
