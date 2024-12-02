# На кассе
CREATE PUBLICATION sales_data FOR TABLE kassa.audit, kassa.balance_operations, kassa.cashier_report, kassa.cashier_shift, kassa.z_report WITH (publish = 'insert');

CREATE SUBSCRIPTION <хостнейм_кассы>
    CONNECTION 'host=<ip_стп> port=5432 dbname=stp user=stp_user password=123qweQWE'
    PUBLICATION master_data;

# На стп
CREATE SUBSCRIPTION <хостнейм_кассы>
    CONNECTION 'host=<ip_кассы> port=5432 dbname=magnumfront user=mfront password=mfront#987'
    PUBLICATION sales_data;

#############################
Если публикации не существует
#############################
create publication master_data FOR TABLE
    kassa.g_access_actions,
    kassa.g_audit_events,
    kassa.g_cash_register,
    kassa.g_certificate_type,
    kassa.g_check_header_body,
    kassa.g_department,
    kassa.g_department_goods,
    kassa.g_error_messages,
    kassa.g_exception_groups,
    kassa.g_front_action,
    kassa.g_goods_addition_unit,
    kassa.g_goods_bar_code,
    kassa.g_goods_fractional_unit,
    kassa.g_goods_info,
    kassa.g_goods_price,
    kassa.g_goods_property,
    kassa.g_option_items,
    kassa.g_option_values,
    kassa.g_payment_card_type,
    kassa.g_price_propocition,
    kassa.g_price_reason,
    kassa.g_return_reason,
    kassa.g_unit,
    kassa.g_user,
    kassa.general_info,
    kassa.hosts,
    promo.bundle_goods,
    promo.client_clubs,
    promo.condition_bundle,
    promo.condition_client,
    promo.condition_coupon,
    promo.condition_goods,
    promo.condition_group_wares,
    promo.condition_prize,
    promo.condition_receipt,
    promo.condition_time,
    promo.prize_group_goods,
    promo.prize_groups,
    promo.promo,
    promo.result_goods,
    promo.result_print;


Проверка

select * from kassa.g_user;

select * from pg_catalog.pg_publication;
select * from pg_catalog.pg_subscription;
select * from pg_stat_replication;

sudo tail -100 /var/log/postgresql/postgresql-15-main.log | grep -i error