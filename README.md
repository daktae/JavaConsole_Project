# JavaConsole_Project
자바콘솔프로젝트

# 프로젝트 소개
 - 파일 입출력 기반의 데이터 처리 자바 콘솔 프로젝트
 - 도서 목록을 조회 및 대출 · 반납 관리를 자동화 기능을 구현

# 개발기간
 - 2023-03-06 ~ 2023-03-11

# :hammer: 기술 스택
### Environment
 <img src="https://img.shields.io/badge/Eclipse IDE-2c2255?style=for-the-badge&logo=Eclipse&logoColor=white"/> 

### Language
<img src="https://img.shields.io/badge/Java-007396?style=for-the-badge&logo=java&logoColor=white"/> 

<hr>

# 사용 데이터 파일

| 데이터파일    | 상세내용                                    |
| ---------- | ---------------------------------------------- |
| bannab.txt | 반납된 도서 목록을 관리하는 데이터 파일                 |
| Book.txt   | 도서관에 등록된 책 목록 데이터 파일 |
| rentLog.txt  | 회원, 비회원이 대출한 책들을 관리하는 데이터 파일 |
| member.txt  | 도서관에 회원가입을 한 회원들 데이터 파일 |


# 기능 구현

## 회원 기능
### 도서 검색
<img src="6. 화면 캡처/회원/책 검색.PNG" width=500>
도서 검색	도서를 검색하면 검색한 도서에 대한 정보(제목, 저자, 출판사, 장르, 수량) 출력하는 기능

### 도서 반납
<img src="6. 화면 캡처/회원/회원 반납.PNG" width=500>
도서 반납	입력한 이름과 전화번호를 대조하여 대출 도서를 반납(반납함 로그에 기록) 연체 시 연체 날짜를 계산하여 연체료를 계산하는 기능

### 퀴즈 참여
<img src="6. 화면 캡처/회원/회원 퀴즈 맞히기.PNG" width=500>
퀴즈 맞히기	로그인한 회원이 등록된 퀴즈에 대해 정답을 입력 및 저장된 정답과 일치할 시 해당 회원의 정보를 저장하는 기능
<hr>

## 관리자
### 도서 등록
<img src="6. 화면 캡처/관리자/관리자 도서 등록.PNG" width=500>
제목, 저자, 출판사, 장르, 가격, 수량을 입력(중복도서 존재 시 수량 추가)하여, 도서 목록에 해당 정보를 기입하는 기능

### 연체 도서 관리
<img src="6. 화면 캡처/관리자/관리자 연체 도서.PNG" width=500>
연체한 회원이 존재 시 그 회원의 연체일수, 연체료를 열람할 수 있는 기능

### 퀴즈 등록
<img src="6. 화면 캡처/관리자/관리자 퀴즈 등록.PNG" width=500>
관리자가 퀴즈의 문제 및 정답을 등록하여 메인 및 회원이 조회 시 문제를 출력하는 기능

## 🔎 핵심 기능 코드
- 도서 검색
```
package com.twlibrary.service;

import java.util.ArrayList;
import java.util.Scanner;

import com.twlibrary.dao.BookDAO;
import com.twlibrary.vo.BookVO;

/**
 * 책 검사를 하는 클래스
 * 메뉴를 통해서 책에 대한 정보를 항목을 골라 검색하는 기능을 구현한 클래스
 * 검색된 책은 BookVO 형태로 return
 * @author 김태완
 * @return BookVO
 */
public class BookSearchService {
	public static BookVO bookSearch() {
		BookVO b = new BookVO();
		boolean loop = true;
		while(loop) {
			Scanner scan = new Scanner(System.in);
			System.out.println();
			System.out.println("────────────────────────────────────");
			System.out.println("책 검색 페이지 입니다.");
			System.out.println("   ");
			System.out.println("  ┌──────[검색  메뉴]────────┐");
			System.out.println("  │                                        │");
			System.out.println("  │   1. 책 제목         2. 저자           │");
			System.out.println("  │   3. 출판사          4. 장르           │");
			System.out.println("  │                                        │");
			System.out.println("  └────────────────────┘");
			System.out.print("메뉴 선택:" );
			String sel = scan.nextLine();
			
			if(sel.equals("1")) {
				// 책 제목
				b=titleSearch();
				loop = false;
			} else if (sel.equals("2")) {
				// 저자
				b=authSearch();
				loop = false;
			} else if (sel.equals("3")) {
				// 출판사
				b=pubSearch();
				loop = false;
			} else if (sel.equals("4")) {
				// 장르
				b=genreSearch();
				loop = false;
			} else {
				System.out.println("1~4 입력");
			}
			
		}
		return b;
		
	}
	/**
	 * 책 정보를 형식에 맞게 정렬하여 출력하는 메소드
	 * @author 김태완
	 */
	public static void printBookInfo(BookVO b) {
		int a = 40;
		System.out.println("───────────────────────────────────────────────────────────────────────────────");
		System.out.println(convert(b.getNum(),3)+"\t"+convert(b.getTitle(),40)+"\t"+convert(b.getAuth(),24)+"\t"+convert(b.getPub(),24)+"\t"+convert(b.getGenre(),8)+"\t"+b.getCount());
		
	}
	private static int getKorCnt(String kor) {
	    int cnt = 0;
	    for (int i = 0 ; i < kor.length() ; i++) {
	        if (kor.charAt(i) >= '가' && kor.charAt(i) <= '힣') {
	            cnt++;
	        }
	    } return cnt;
	}
	/**
	 * 책 정보를 한글이든 영어든 글자수에 맞게 정렬해주는 메소드
	 * 한글은 전각문자라, 글자열 크기에 -1을 해준다.
	 * @return String
	 */
	public static String convert(String word, int size) {
	    String formatter = String.format("%%%ds", size -getKorCnt(word));
	    return String.format(formatter, word);
	}	
	public static void printBookCategory() {
		String num = "[책번호]";
		String title = "[제목]";
		String auth = "[저자]";
		String pub = "[출판사]";
		String genre = "[장르]";
		String count = "[수량]";
		System.out.println("───────────────────────────────────────────────────────────────────────────────");
		System.out.printf("%-4s\t\t%-32s\t\t%-24s\t%-15s%-8s%-2s\n"
				,num
				,title
				,auth
				,pub
				,genre
				,count);
	}
	/**
	 * 입력받은 제목을 반복문을 통해서 BookDAO를 통해 읽어들인 book.txt의 제목들과 비교
	 * contains을 사용하여 제목에 있는 키워드가 존재하면 출력하고 정보를 ArrayList형태로 저장 
	 * 그리고 BookVO로 리턴하기 위해서 입력받은 번호와 list에 저장된 getNum과 비교
	 * 비교하여 선택된 책을 BookVO로 리턴
	 * @author 김태완
	 * @return BookVO
	 */
	//제목 검색
	public static BookVO titleSearch() { 
		Scanner scan = new Scanner(System.in);
		ArrayList<BookVO> list = new ArrayList<BookVO>();
		boolean flag = true;
		while(flag) {
			System.out.print("책 제목: ");
			String input = scan.nextLine();
			printBookCategory();
			
			for(BookVO b : BookDAO.getList()) {
				if(b.getTitle().contains(input)) {
					printBookInfo(b);
					list.add(b);
					flag=false;
				} 
				
				
			}
			if(list.size()==0) {
				System.out.println("책 결과가 없습니다.");
				
			}
		}
		flag=true;
		System.out.println();
		System.out.println();

		while(flag) {
			System.out.println("───────────────────────────────────────────────────────────────────────────────");
			System.out.print("책 번호를 입력해주세요. : ");
			String num = scan.nextLine();
			BookVO b = new BookVO();
			for(int i=0; i<list.size(); i++) {
				if(num.equals(list.get(i).getNum())) {
					b=list.get(i);
				}
			}
			if(num.equals(b.getNum())) {
				printBookInfo(b);
				return b;			
			} else {
				System.out.println("숫자를 다시 입력해주세요");
			}
		}
		
		return null;
	}
	/**
	 * 입력받은 저자를 반복문을 통해서 BookDAO를 통해 읽어들인 book.txt의 저자들과 비교
	 * 비교하여 일치한 것을 ArrayList 형태로 저장
	 * 그리고 BookVO로 리턴하기 위해서 입력받은 번호와 list에 저장된 getNum과 비교
	 * 비교하여 선택된 책을 BookVO로 리턴
	 * @author 김태완
	 * @return BookVO
	 */
	//저자 검색
	public static BookVO authSearch() { 
		Scanner scan = new Scanner(System.in);
		ArrayList<BookVO> list = new ArrayList<BookVO>();
		boolean flag = true;
		while(flag) {
			System.out.print("책 저자: ");
			String input = scan.nextLine();
			printBookCategory();
			for(BookVO b : BookDAO.getList()) {
				if(input.equals(b.getAuth())) {
					printBookInfo(b);
					list.add(b);
					flag=false;
				} 
				
			}
			if(list.size()==0) {
				System.out.println("책 결과가 없습니다.");
				
			}
			
		}
		flag=true;
		System.out.println();
		System.out.println();
		while(flag) {
			System.out.println("───────────────────────────────────────────────────────────────────────────────");
			System.out.print("책 번호를 입력해주세요. : ");
			String num = scan.nextLine();
			BookVO b = new BookVO();
			for(int i=0; i<list.size(); i++) {
				if(num.equals(list.get(i).getNum())) {
					b=list.get(i);
					
				}
			}
			if(num.equals(b.getNum())) {
				printBookInfo(b);
				return b;			
			} else {
				System.out.println("숫자를 다시 입력해주세요");
			}
		}
		return null;
		
	}
	/**
	 * 입력받은 출판사를 반복문을 통해서 BookDAO를 통해 읽어들인 book.txt의 출판사들과 비교
	 * 비교하여 일치한 것을 ArrayList 형태로 저장
	 * 그리고 BookVO로 리턴하기 위해서 입력받은 번호와 list에 저장된 getNum과 비교
	 * 비교하여 선택된 책을 BookVO로 리턴
	 * @author 김태완
	 * @return BookVO
	 */
	//출판사 검색
	public static BookVO pubSearch() { 
		Scanner scan = new Scanner(System.in);
		ArrayList<BookVO> list = new ArrayList<BookVO>();
		boolean flag = true;
		while(flag) {
			System.out.print("출판사: ");
			String input = scan.nextLine();
			printBookCategory();
			for(BookVO b : BookDAO.getList()) {
				if(input.equals(b.getPub())) {
					printBookInfo(b);
					list.add(b);
					flag=false;
				} 
				
			}
			if(list.size()==0) {
				System.out.println("책 결과가 없습니다.");
				
			}
			
		}
		flag=true;
		System.out.println();
		System.out.println();
		while(flag) {
			System.out.println("───────────────────────────────────────────────────────────────────────────────");
			System.out.print("책 번호를 입력해주세요. : ");
			String num = scan.nextLine();
			BookVO b = new BookVO();
			for(int i=0; i<list.size(); i++) {
				if(num.equals(list.get(i).getNum())) {
					b=list.get(i);
				}
			}
			if(num.equals(b.getNum())) {
				printBookInfo(b);
				return b;			
			} else {
				System.out.println("숫자를 다시 입력해주세요");
			}
		}
		return null;
	}
	/**
	 * 입력받은 장르를 반복문을 통해서 BookDAO를 통해 읽어들인 book.txt의 장르들과 비교
	 * 비교하여 일치한 것을 ArrayList 형태로 저장
	 * 그리고 BookVO로 리턴하기 위해서 입력받은 번호와 list에 저장된 getNum과 비교
	 * 비교하여 선택된 책을 BookVO로 리턴
	 * @author 김태완
	 * @return BookVO
	 */
	//장르 검색
	public static BookVO genreSearch() {
		Scanner scan = new Scanner(System.in);
		ArrayList<BookVO> list = new ArrayList<BookVO>();
		boolean flag = true;
		while(flag) {
			System.out.print("책 장르: ");
			String input = scan.nextLine();
			printBookCategory();
			for(BookVO b : BookDAO.getList()) {
				if(input.equals(b.getGenre())) {
					printBookInfo(b);
					list.add(b);
					flag=false;
				} 
				
			}
			if(list.size()==0) {
				System.out.println("책 결과가 없습니다.");
				
			}
			
		}
		flag=true;
		System.out.println();
		System.out.println();
		while(flag) {
			System.out.println("───────────────────────────────────────────────────────────────────────────────");
			System.out.print("책 번호를 입력해주세요. : ");
			String num = scan.nextLine();
			BookVO b = new BookVO();
			for(int i=0; i<list.size(); i++) {
				if(num.equals(list.get(i).getNum())) {
					b=list.get(i);
				}
			}
			if(num.equals(b.getNum())) {
				printBookInfo(b);
				return b;			
			} else {
				System.out.println("숫자를 다시 입력해주세요");
			}
		}
		return null;
	}
}


```

## 문제해결을 위한 노력
- 웹을 배우지 않은 단계라서, 모든 ui를 자바콘솔로 한땀한땀 구현해야했던 것이 문제였지만

## :memo: 아쉬운 점
- 웹을 배우지 않은 단계라서, 모든 ui를 자바 콘솔 

