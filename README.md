DROP PROCEDURE IF EXISTS ���ΰ���;
DELIMITER $$
CREATE DEFINER=`root`@`%` PROCEDURE `���ΰ���`(A2 DATE, B2 DATE)
BEGIN
select hyperlink('https://wing.coupang.com/vendor-inventory/list#searchIds=',z.`�ɼ�ID`) �ɼ�ID, concat('=hyperlink("https://www.coupang.com/vp/products/', z.�����ǰID,'?vendorItemId=',z.�ɼ�ID,'","',z.�����ǰID,'")') �����ǰID, z.`��ǰ�ڵ�`,z.����ȣ,z.`��� ��ǰ��`,z.`��� �ɼǸ�`,z.`���� ���ذ�`,z.`���� �ǸŰ�`,z.`���� ��ۺ�`,z.`���� ���`,z.`���� �Ǹŷ�`,z.`����������ȣ` �������������ȣ, z.`�������`,z.`�������밡`,z.`���� ����`,z.`���� ���۰�`,z.`���� �ǸŰ�`,z.`���� ���ذ�`,z.`���� ���`, z.`�Ǹż�����`,z.`��������`,z.`�������ͷ�`, z.`����������`, z.`������� �����Ͻ�`,z.�����ǰ����, IF(z.`�����ǰ����`*(1-z.�������)>z.`���� ����`, round(z.�����ǰ����,-2)+90, z.`���� ���۰�`-10) ��õ����, z.`���� �ǸŰ�`- IF(z.`�����ǰ����`*(1-z.�������)>z.`���� ����`, round(z.�����ǰ����,-2)+90, z.`���� ���۰�`-10) ��õ���ξ�, (IF(z.`�����ǰ����`*(1-z.�������)>z.`���� ����`, round(z.�����ǰ����,-2)+90, z.`���� ���۰�`-10)*(1-z.�������)-z.`���� ����`)/IF(z.`�����ǰ����`*(1-z.�������)>z.`���� ����`, round(z.�����ǰ����,-2)+90, z.`���� ���۰�`-1) ��������,  z.`�����ǰURL`, z.`�������`, z.`�Ǹż���`, z.`�����`,e.�����,  e.�����, e.Ŭ����, e.���ֹ��� ������ȯ, e.Ŭ����,e.������ȯ��,e.�Ѹ��� �������,z.`����ǸŰ�`, z.`��ġ��ŷ ��ǰ����`,z.`��ġ��ŷ ��ǰ��ȸ`
from 
(
    select *
    from 
    (
        select a.`�ɼ�ID`,a.`�����ǰID`, a.`��ǰ�ڵ�`,a.`��ġ��ŷ ��ǰ����`,a.`��ġ��ŷ ��ǰ��ȸ`,a.`��� ��ǰ��`,a.`��� �ɼǸ�`,a.`����ó`,a.`���� ����`,a.`���� ���ذ�`,a.`���� �ǸŰ�` ,a.`���� ��ۺ�`, a.`���� ���ذ�`,a.`���� �ǸŰ�`,a.`���� ���`,a.`���� ���۰�`,a.`�����ܿ�����(���)` `���� ���`,a.`���� �Ǹŷ�`,a.`�������`,a.`�������밡`,a.`�Ǹż�����`,a.`��������`,a.`�������ͷ�`, a.`����������`, a.`������� �����Ͻ�`,a.`������`,a.`deal_seq` ����ȣ, o.bm_price �����ǰ����, a.competitor '�����ǰURL', a.`commission_coupang` �������, a.����������ȣ
        from 
        (
            select 
            c.option_id '�ɼ�ID' ,
            c.product_id '�����ǰID',
            c.contract_seq '��ǰ�ڵ�', 
            hyperlink('https://sell.smartstore.naver.com/#/products/edit/',d.`smartstore_admin_no`) '��ġ��ŷ ��ǰ����',
            hyperlink('https://smartstore.naver.com/farmers_net/products/', d.`smartstore_no`) '��ġ��ŷ ��ǰ��ȸ',
            c.goods_title '��� ��ǰ��', 
            c.option_title '��� �ɼǸ�', 
            t.supplier_name ����ó,
            t.buying_price+f.fee '���� ����', 
            c.normal_price '���� ���ذ�',
            c.price '���� �ǸŰ�', 
            d.`delivery_fee_coupang` '���� ��ۺ�',
            round(get_price(d.delivery_fee_coupang, t.buying_price, d.margin + 0.2, f.fee ),-3) '���� ���ذ�', 
            get_price(d.delivery_fee_coupang, t.buying_price, d.margin, f.fee) '���� �ǸŰ�', 
            round(p.stock/t.quantity,0) '���� ���', 
            round(get_price(d.delivery_fee_coupang, t.buying_price, d.commission_coupang+0.05, f.fee),-2) '���� ���۰�', 
            c.stocks '�����ܿ�����(���)',
            c.sold '���� �Ǹŷ�',
            �������ξ�(m.���ι��,m.������,m.���αݾ�,m.������,m.������) �������, 
            c.price + d.`delivery_fee_coupang`-�������ξ�(m.���ι��,m.������,m.���αݾ�,m.������,m.������) �������밡,
            round((c.price + d.`delivery_fee_coupang`-�������ξ�(m.���ι��,m.������,m.���αݾ�,m.������,m.������))*d.commission_coupang,0) �Ǹż�����,
            c.price + d.`delivery_fee_coupang`-�������ξ�(m.���ι��,m.������,m.���αݾ�,m.������,m.������)-round((c.price + d.`delivery_fee_coupang`-�������ξ�(m.���ι��,m.������,m.���αݾ�,m.������,m.������))*d.commission_coupang,0)-t.buying_price-f.fee ��������, 
            ( c.price + d.`delivery_fee_coupang`-�������ξ�(m.���ι��,m.������,m.���αݾ�,m.������,m.������)-round((c.price + d.`delivery_fee_coupang`-�������ξ�(m.���ι��,m.������,m.���αݾ�,m.������,m.������))*d.commission_coupang,0)-t.buying_price-f.fee)/ (c.price + d.`delivery_fee_coupang`-�������ξ�(m.���ι��,m.������,m.���αݾ�,m.������,m.������))  �������ͷ�,
            m.����������,STR_TO_DATE(REPLACE(m.������, ' GMT+09:00', ''), '%a %b %d %T %Y') `������� �����Ͻ�`,STR_TO_DATE(REPLACE(m.������, ' GMT+09:00', ''), '%a %b %d %T %Y') ������, 
            d.deal_seq, t.contract_seq, d.`competitor`, d.commission_coupang,
            m.����������ȣ
            from  fp_contract t, fp_deal d, fp_purchase p, fp_supplier s, fp_delivery_fee f, d_coupang c 
            left join ������ǰ m
            on c.option_id = m.�ɼ�ID and m.�������� = '�������' and m.������������ = 'APPLIED' 
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
        select g.option_id, sum(g.quantity) �Ǹż���, sum(g.paid_price) �����,  round(sum(g.paid_price) / sum(g.quantity),0) ����ǸŰ�
        from o_coupang g
        where g.paid_datetime >= A2
        and g.paid_datetime <= date_add(B2, interval 1 day)
        group by option_id
    ) y
    on x.`�ɼ�ID` = y.option_id 
) z left join 
(
    select r.��������ɼ�ID, sum(r.�����) �����, sum(r.Ŭ����) Ŭ����, sum(r.���ֹ���) ���ֹ���,sum(r.���ֹ���)/sum(r.Ŭ����) ������ȯ��, sum(r.�����) �����, sum(r.Ŭ����)/sum(r.�����) Ŭ����, sum(r.����ȯ�����) �Ѹ���, sum(r.����ȯ�����)/sum(r.�����) '���� ���ͷ�'
    from ���ε�_���λ�ǰ������ r
    where r.��¥ >= A2
    and r.��¥ <= B2
    group by r.��������ɼ�ID
) e on z.option_id = e.��������ɼ�ID
order by z.`��� ��ǰ��`, z.`��ǰ�ڵ�`;
END$$
DELIMITER ;