---
layout: default
title: "Sagers-Day-1"
nav_order: 1
parent: "Sagers 개발 일지"
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# Github 쓸려다가 실패

백업을 많이 해야한다는 약간 정신병이 있는데 NAS와 Github 를 같이 쓸 생각을 했음.<br/>그래서 NAS랑 Github 를 동시에 올리려고 계획하고 했는데 이래서안되고 저래서 안된다고 거부깃이 친절하게 알려준다.<br/>결국엔 NAS 에다가만 업로드 하기로 하긴 했는데... 그 이유는

## Github LFS(LargeFileSystem)

Git 은 소스 코드 관리를 주 목적으로 만들어짐<br/>
=> 큰 파일을 관리할 목적으로는 적절치 않음<br/>
=> Github 에서는 100MB 이상 파일 Push 시에 차단함. [Git-Largefile-Policy](https://docs.github.com/en/repositories/working-with-files/managing-large-files/about-large-files-on-github)<br/>
=> 하지만 또 친절하게 Github에서 LFS 라고 대용량 파일을 업로드 할 수 있게 만들어 놨다. [참고 사이트](https://leimao.github.io/blog/Git-Large-File-Storage/)<br/>
<br/>

## NAS Git Server 문제

위에서 LFS 설정을 해버리닌까 NAS Git 서버가 바보됨<br/>
=> LFS가 설정된 Repo는 Push 하면서 뭔가를 더 하나본데 거기서 LFS에서 필요한 기능을 NAS Git Server 는 못찾겠다고 난리침<br/>
=> 그래서 해결법을 찾던 중 그냥 상위 폴더에서 이 프로젝트 전체를 포함하게 해버림<br/>
=> 의외로 잘 동작하고 Super-pointer? 라는 이상한 분홍색 브랜치가 보임. NAS에서 찍는 좌표가 이렇게 보이는 듯 하다.<br/>
=> 그렇게 서브에선 Github로 백업, 전체 프로젝트는 NAS에 백업할 수 있는 듯 했다.<br/>
<br/>

## LFS는 유료다

테스트 삼아 임시 파일을 생성하고 Github에 Push<br/>
=> Github에서 갑자기 돈 내라고함<br/>
=> 왜 몇시간동안 이 짓거리 했는지 모르겠다. 빠르게 포기. NAS 에만 업로드<br/>
<br/>