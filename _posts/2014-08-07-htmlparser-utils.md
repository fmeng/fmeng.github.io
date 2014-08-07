---
layout: post
title: "HtmlParser and cookie utils"
categories:
- java
---

## HtmlParser -utils ##
	
	package com.hellopipi.chosing.utils;
	
	import java.util.ArrayList;
	import java.util.List;
	
	import org.htmlparser.Node;
	import org.htmlparser.NodeFilter;
	import org.htmlparser.Parser;
	import org.htmlparser.nodes.TagNode;
	import org.htmlparser.util.NodeList;
	import org.htmlparser.util.ParserException;
	
	public class ParserTag {
	
		/**
		 * 用于提取一个String中的tag节点，返回list
		 * @param page
		 * @param tagType
		 * @param attributeName
		 * @param attributeValue
		 * @return
		 * @throws Exception
		 */
		private ParserTag(){}
		//三个参数都有，返回一个list
		public  static <T extends TagNode> List<T> getTagList (String page ,final Class<T> tagType, final String attributeName, final String attributeValue){
			Parser parser = new Parser();
			try {
				parser.setInputHTML(page);
			} catch (ParserException e) {
				throw new RuntimeException(e);
			}
			List<T> tags = new ArrayList<T>();
			NodeList tagList = null;
			try {
				tagList = parser.parse(new NodeFilter() {
					
					@Override
					public boolean accept(Node node) {
						if(node.getClass() == tagType){
							T tn = (T)node;
							//指定自己的规则
							String value = tn.getAttribute(attributeName);
							if(value != null && attributeValue.equals(value)){
								return true;
							}
						}
						return false;
					}
				});
			} catch (ParserException e) {
				throw new RuntimeException(e);
			}
			for(int i=0; i<tagList.size(); i++){
				T t = (T)tagList.elementAt(i);
				tags.add(t);
			}
			return tags;
		}
		
		//只有一个tagType参数，返回一个list
		public  static <T extends TagNode> List<T> getTagList (String page ,final Class<T> tagType ){
			Parser parser = new Parser();
			try {
				parser.setInputHTML(page);
			} catch (ParserException e) {
				throw new RuntimeException(e);
			}
			List<T> tags = new ArrayList<T>();
			NodeList tagList = null;
			try {
				tagList = parser.parse(new NodeFilter() {
					
					@Override
					public boolean accept(Node node) {
						if(node.getClass() == tagType){
							//指定自己的规则
							return true;
						}
						return false;
					}
				});
			} catch (ParserException e) {
				
				throw new RuntimeException(e);
			}
			for(int i=0; i<tagList.size(); i++){
				T t = (T)tagList.elementAt(i);
				tags.add(t);
			}
			return tags;
		}
		
		//三个参数都有，返回一个TagNode
			public  static <T extends TagNode> TagNode getOneTagNode (String page ,final Class<T> tagType, final String attributeName, final String attributeValue) {
				Parser parser = new Parser();
				try {
					parser.setInputHTML(page);
				} catch (ParserException e) {
					throw new RuntimeException(e);
				}
				NodeList tagList;
				try {
					tagList = parser.parse(new NodeFilter() {
						
						@Override
						public boolean accept(Node node) {
							if(node.getClass() == tagType){
								T tn = (T)node;
								//指定自己的规则
								String value = tn.getAttribute(attributeName);
								if(value != null && attributeValue.equals(value)){
									return true;
								}
							}
							return false;
						}
					});
				} catch (ParserException e) {
					throw new RuntimeException(e);
				}
				TagNode t = (TagNode)tagList.elementAt(0);
				return t;
			}
			
			
			//只有一个tagType，返回一个TagNode
				public  static <T extends TagNode> TagNode getOneTagNode (String page ,final Class<T> tagType){
					Parser parser = new Parser();
					try {
						parser.setInputHTML(page);
					} catch (ParserException e) {
						// TODO Auto-generated catch block
						throw new RuntimeException(e);
					}
					NodeList tagList = null;
					try {
						tagList = parser.parse(new NodeFilter() {
							
							@Override
							public boolean accept(Node node) {
								if(node.getClass() == tagType){
									//指定自己的规则
									return true;
								}
								return false;
							}
						});
					} catch (ParserException e) {
						throw new RuntimeException(e);
					}
					TagNode t = (TagNode)tagList.elementAt(0);
					return t;
				}
			
	}
	
	
	
	

## Cookie -utils ##



	package com.hellopipi.chosing.utils;
	
	import java.util.List;
	
	import org.apache.http.client.protocol.HttpClientContext;
	import org.apache.http.cookie.Cookie;
	import org.apache.http.impl.cookie.BasicClientCookie2;
	
	public class HttpCookie {
		private HttpCookie (){}
		
		public static List<Cookie> getCookies(HttpClientContext context){
			List<Cookie> list = null;
			list = context.getCookieStore().getCookies();
			return list;
		}
		
		public static Cookie getOneCookie(HttpClientContext context){
			List<Cookie> list = null;
			list = context.getCookieStore().getCookies();
			Cookie c = list.get(0);
			return c;
		}
		
		public static String getOneCookieValue(HttpClientContext context ,String cookieName){
			List<Cookie> list = null;
			list = context.getCookieStore().getCookies();
			for(int i=0; i<list.size();i++){
				Cookie c = list.get(i);
				if(cookieName.equals(c.getName())){
					return c.getValue();
				}
			}
			return null;
		}
		
		public static BasicClientCookie2 createOneClientCookie(String host,String path,String name,String value){
			BasicClientCookie2 stdCookie = new BasicClientCookie2("name", "value");
			stdCookie.setVersion(1);
			stdCookie.setDomain(".mycompany.com");
			stdCookie.setPorts(new int[] {80,8080});
			stdCookie.setPath("/");
			return stdCookie;
		}
	}


