---
layout: post
title: "Borrado batch en Grails"
date: 2015-05-01 20:58:30 -0500
published: false
comments: true
categories:
---

import org.hibernate.FetchMode as FM import grails.gorm.*

all = [] CollaborationLink.list().each { link -> if(link.participants.isEmpty()) all << link } all*.delete() println all.size()

// ****************************

CollaborationLink.where { participants.size() == 0 }.deleteAll() // No soporta el método cuando se busca por la relación

// **************************** def criteria = new DetachedCriteria(CollaborationLink).build { isEmpty 'participants' }

println criteria.deleteAll() println criteria.count()
