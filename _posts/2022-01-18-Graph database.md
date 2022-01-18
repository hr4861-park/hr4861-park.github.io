---
title: "Graph Database"
date: 2022-01-18T11:14:56Z
categories:
  - database
tags:
  - database
  - graph
---


# 그래프 데이터베이스?

- 데이터 표현 및 저장을 위해 nodes, edges, properties 이 포함된 그래프 구조를 사용하는 데이터 베이스
- NoSQL 데이터베이스의 한 종류
- 두가지 그래프 모델을 사용한다.
  - LPG(Labeled property graph)
  - RDF(resource description framework)

## LPG(Labeled property graph)

- G = (V, E) = (Vertex, Edge) = (Node, Relationship)
- Edge 에는 방향이 존재
- Node와 Edge의 타입을 **Label** 이라고 한다.
- Node와 Edge의 Key/value tuple 을 **Property** 라고 한다.

## RDB와의 비교

|RDB|GDB|
|--|--|
|Table|Label|
|Row|Node|
|Column|Property|
|Foreign Key|Relationship|

## 장점

- 유연한 스키마
- 조인과 메모리사용 없이 노드간 순회 가능
- 직관적인 구조