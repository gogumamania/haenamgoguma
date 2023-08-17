DROP PROCEDURE IF EXISTS 쿠팡가격;
DELIMITER $$
CREATE DEFINER=`root`@`%` PROCEDURE `쿠팡가격`(A2 DATE, B2 DATE)
BEGIN
select hyperlink('https://wing.coupang.com/vendor-inventory/list#searchIds=',z.`옵션ID`) 옵션ID, concat('=hyperlink("https://www.coupang.com/vp/products/', z.노출상품ID,'?vendorItemId=',z.옵션ID,'","',z.노출상품ID,'")') 노출상품ID, z.`상품코드`,z.딜번호,z.`등록 상품명`,z.`등록 옵션명`,z.`현재 기준가`,z.`현재 판매가`,z.`현재 배송비`,z.`현재 재고`,z.`누적 판매량`,z.`할인쿠폰번호` 즉시할인쿠폰번호, z.`즉시할인`,z.`할인적용가`,z.`산출 원가`,z.`산출 시작가`,z.`산출 판매가`,z.`산출 기준가`,z.`산출 재고`, z.`판매수수료`,z.`매출이익`,z.`매출이익률`, z.`할인쿠폰명`, z.`즉시할인 시작일시`,z.경쟁상품가격, IF(z.`경쟁상품가격`*(1-z.수수료률)>z.`산출 원가`, round(z.경쟁상품가격,-2)+90, z.`산출 시작가`-10) 추천가격, z.`현재 판매가`- IF(z.`경쟁상품가격`*(1-z.수수료률)>z.`산출 원가`, round(z.경쟁상품가격,-2)+90, z.`산출 시작가`-10) 추천할인액, (IF(z.`경쟁상품가격`*(1-z.수수료률)>z.`산출 원가`, round(z.경쟁상품가격,-2)+90, z.`산출 시작가`-10)*(1-z.수수료률)-z.`산출 원가`)/IF(z.`경쟁상품가격`*(1-z.수수료률)>z.`산출 원가`, round(z.경쟁상품가격,-2)+90, z.`산출 시작가`-1) 예상마진률,  z.`경쟁상품URL`, z.`수수료률`, z.`판매수량`, z.`매출액`,e.광고비,  e.노출수, e.클릭수, e.총주문수 구매전환, e.클릭률,e.구매전환률,e.총매출 광고매출,z.`평균판매가`, z.`벤치마킹 상품수정`,z.`벤치마킹 상품조회`
from 
(
    select *
    from 
    (
        select a.`옵션ID`,a.`노출상품ID`, a.`상품코드`,a.`벤치마킹 상품수정`,a.`벤치마킹 상품조회`,a.`등록 상품명`,a.`등록 옵션명`,a.`매입처`,a.`산출 원가`,a.`현재 기준가`,a.`현재 판매가` ,a.`현재 배송비`, a.`산출 기준가`,a.`산출 판매가`,a.`산출 재고`,a.`산출 시작가`,a.`현재잔여수량(재고)` `현재 재고`,a.`누적 판매량`,a.`즉시할인`,a.`할인적용가`,a.`판매수수료`,a.`매출이익`,a.`매출이익률`, a.`할인쿠폰명`, a.`즉시할인 시작일시`,a.`종료일`,a.`deal_seq` 딜번호, o.bm_price 경쟁상품가격, a.competitor '경쟁상품URL', a.`commission_coupang` 수수료률, a.할인쿠폰번호
        from 
        (
            select 
            c.option_id '옵션ID' ,
            c.product_id '노출상품ID',
            c.contract_seq '상품코드', 
            hyperlink('https://sell.smartstore.naver.com/#/products/edit/',d.`smartstore_admin_no`) '벤치마킹 상품수정',
            hyperlink('https://smartstore.naver.com/farmers_net/products/', d.`smartstore_no`) '벤치마킹 상품조회',
            c.goods_title '등록 상품명', 
            c.option_title '등록 옵션명', 
            t.supplier_name 매입처,
            t.buying_price+f.fee '산출 원가', 
            c.normal_price '현재 기준가',
            c.price '현재 판매가', 
            d.`delivery_fee_coupang` '현재 배송비',
            round(get_price(d.delivery_fee_coupang, t.buying_price, d.margin + 0.2, f.fee ),-3) '산출 기준가', 
            get_price(d.delivery_fee_coupang, t.buying_price, d.margin, f.fee) '산출 판매가', 
            round(p.stock/t.quantity,0) '산출 재고', 
            round(get_price(d.delivery_fee_coupang, t.buying_price, d.commission_coupang+0.05, f.fee),-2) '산출 시작가', 
            c.stocks '현재잔여수량(재고)',
            c.sold '누적 판매량',
            쿠팡할인액(m.할인방식,m.할인율,m.할인금액,m.시작일,m.종료일) 즉시할인, 
            c.price + d.`delivery_fee_coupang`-쿠팡할인액(m.할인방식,m.할인율,m.할인금액,m.시작일,m.종료일) 할인적용가,
            round((c.price + d.`delivery_fee_coupang`-쿠팡할인액(m.할인방식,m.할인율,m.할인금액,m.시작일,m.종료일))*d.commission_coupang,0) 판매수수료,
            c.price + d.`delivery_fee_coupang`-쿠팡할인액(m.할인방식,m.할인율,m.할인금액,m.시작일,m.종료일)-round((c.price + d.`delivery_fee_coupang`-쿠팡할인액(m.할인방식,m.할인율,m.할인금액,m.시작일,m.종료일))*d.commission_coupang,0)-t.buying_price-f.fee 매출이익, 
            ( c.price + d.`delivery_fee_coupang`-쿠팡할인액(m.할인방식,m.할인율,m.할인금액,m.시작일,m.종료일)-round((c.price + d.`delivery_fee_coupang`-쿠팡할인액(m.할인방식,m.할인율,m.할인금액,m.시작일,m.종료일))*d.commission_coupang,0)-t.buying_price-f.fee)/ (c.price + d.`delivery_fee_coupang`-쿠팡할인액(m.할인방식,m.할인율,m.할인금액,m.시작일,m.종료일))  매출이익률,
            m.할인쿠폰명,STR_TO_DATE(REPLACE(m.시작일, ' GMT+09:00', ''), '%a %b %d %T %Y') `즉시할인 시작일시`,STR_TO_DATE(REPLACE(m.종료일, ' GMT+09:00', ''), '%a %b %d %T %Y') 종료일, 
            d.deal_seq, t.contract_seq, d.`competitor`, d.commission_coupang,
            m.할인쿠폰번호
            from  fp_contract t, fp_deal d, fp_purchase p, fp_supplier s, fp_delivery_fee f, d_coupang c 
            left join 쿠폰상품 m
            on c.option_id = m.옵션ID and m.쿠폰종류 = '즉시할인' and m.할인쿠폰상태 = 'APPLIED' 
            where c.contract_seq = t.contract_seq 
            and c.coupang_id = d.coupang_id 
            and t.supplier_name = p.supplier_name 
            and t.goods_name = p.goods_name 
            and t.criteria = p.criteria 
            and t.unit = p.unit
            and p.`supplier_name` = s.`supplier_name`
            and f.courier = s.courier
            and f.kg = CEIL(t.parcel_quantity)
        ) a left join fp_option o 
        on a.deal_seq = o.deal_seq
        and a.contract_seq = o.contract_seq
    ) x left join 
    (
        select g.option_id, sum(g.quantity) 판매수량, sum(g.paid_price) 매출액,  round(sum(g.paid_price) / sum(g.quantity),0) 평균판매가
        from o_coupang g
        where g.paid_datetime >= A2
        and g.paid_datetime <= date_add(B2, interval 1 day)
        group by option_id
    ) y
    on x.`옵션ID` = y.option_id 
) z left join 
(
    select r.광고집행옵션ID, sum(r.노출수) 노출수, sum(r.클릭수) 클릭수, sum(r.총주문수) 총주문수,sum(r.총주문수)/sum(r.클릭수) 구매전환률, sum(r.광고비) 광고비, sum(r.클릭수)/sum(r.노출수) 클릭률, sum(r.총전환매출액) 총매출, sum(r.총전환매출액)/sum(r.광고비) '광고 수익률'
    from 업로드_쿠팡상품광고보고서 r
    where r.날짜 >= A2
    and r.날짜 <= B2
    group by r.광고집행옵션ID
) e on z.option_id = e.광고집행옵션ID
order by z.`등록 상품명`, z.`상품코드`;
END$$
DELIMITER ;