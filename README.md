# Woocommerce - Ordenar Produtos por Disponibilidade

Este código permite ordenar os produtos do WooCommerce com base na quantidade disponível em estoque. Os produtos disponíveis serão listados primeiro, seguidos pelos produtos fora de estoque.

## Instalação

Para instalar, adicione o código a seu arquivo `functions.php` do tema WordPress.

```php

// Ordenando produtos por quantidade em estoque, com produtos disponíveis primeiro
class DWP_Orderby_Stock_Status {
    public function __construct(){
        // Verifica se o WooCommerce está instalado e ativo
        if (in_array('woocommerce/woocommerce.php', apply_filters('active_plugins', get_option('active_plugins')))) {
            add_filter('posts_clauses', array($this, 'order_by_stock_status'), 2000);
        }
    }
    
    public function order_by_stock_status($posts_clauses){
        global $wpdb;
        // Altera a ordem apenas na listagem dos produtos
        if (is_woocommerce() && (is_shop() || is_product_category() || is_product_tag())) {
            $posts_clauses['join'] .= " INNER JOIN $wpdb->postmeta istockstatus ON ($wpdb->posts.ID = istockstatus.post_id) ";
            $posts_clauses['orderby'] = " istockstatus.meta_value ASC, " . $posts_clauses['orderby'];
            $posts_clauses['where'] = " AND istockstatus.meta_key = '_stock_status' AND istockstatus.meta_value <> '' " . $posts_clauses['where'];
        }
        return $posts_clauses;
    }
}

new DWP_Orderby_Stock_Status;
