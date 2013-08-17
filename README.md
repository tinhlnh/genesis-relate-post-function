add_image_size( 'related', 120, 100, true );
//for XHTML themes
add_action( 'genesis_after_post_content', 'child_related_posts' );
//for HTML5 themes
add_action( 'genesis_after_entry_content', 'child_related_posts' );

/**
 * Outputs related posts with thumbnail
 *
 */
function child_related_posts() {

    if ( is_single ( ) ) {

        global $post;

        $count = 0;
        $postIDs = array( $post-&gt;ID );
        $related = '';
        $tags = wp_get_post_tags( $post-&gt;ID );
        $cats = wp_get_post_categories( $post-&gt;ID );

        if ( $tags ) {

            foreach ( $tags as $tag ) {

                $tagID[] = $tag-&gt;term_id;

            }

            $args = array(
                'tag__in'               =&gt; $tagID,
                'post__not_in'          =&gt; $postIDs,
                'showposts'             =&gt; 4,
                'ignore_sticky_posts'   =&gt; 1,
                'tax_query'             =&gt; array(
                    array(
                                        'taxonomy'  =&gt; 'post_format',
                                        'field'     =&gt; 'slug',
                                        'terms'     =&gt; array(
                                            'post-format-link',
                                            'post-format-status',
                                            'post-format-aside',
                                            'post-format-quote'
                                            ),
                                        'operator'  =&gt; 'NOT IN'
                    )
                )
            );

            $tag_query = new WP_Query( $args );

            if ( $tag_query-&gt;have_posts() ) {

                while ( $tag_query-&gt;have_posts() ) {

                    $tag_query-&gt;the_post();

                    $img = genesis_get_image() ? genesis_get_image( array( 'size' =&gt; 'related' ) ) : '&lt;img alt="' . get_the_title() . '" src="' . get_bloginfo( 'stylesheet_directory' ) . '/images/related.png" /&gt;';

                    $related .= '&lt;/pre&gt;
&lt;ul&gt;
	&lt;li&gt;&lt;a title="Permanent Link to' . get_the_title() . '" href="' . get_permalink() . '" rel="bookmark"&gt;' . $img . get_the_title() . '&lt;/a&gt;&lt;/li&gt;
&lt;/ul&gt;
&lt;pre&gt;

';

                    $postIDs[] = $post-&gt;ID;

                    $count++;
                }
            }
        }

        if ( $count &lt;= 3 ) {             $catIDs = array( );             foreach ( $cats as $cat ) {                 if ( 3 == $cat )                     continue;                 $catIDs[] = $cat;             }             $showposts = 4 - $count;             $args = array(                 'category__in'          =&gt; $catIDs,
                'post__not_in'          =&gt; $postIDs,
                'showposts'             =&gt; $showposts,
                'ignore_sticky_posts'   =&gt; 1,
                'orderby'               =&gt; 'rand',
                'tax_query'             =&gt; array(
                                    array(
                                        'taxonomy'  =&gt; 'post_format',
                                        'field'     =&gt; 'slug',
                                        'terms'     =&gt; array(
                                            'post-format-link',
                                            'post-format-status',
                                            'post-format-aside',
                                            'post-format-quote' ),
                                        'operator' =&gt; 'NOT IN'
                                    )
                )
            );

            $cat_query = new WP_Query( $args );

            if ( $cat_query-&gt;have_posts() ) {

                while ( $cat_query-&gt;have_posts() ) {

                    $cat_query-&gt;the_post();

                    $img = genesis_get_image() ? genesis_get_image( array( 'size' =&gt; 'related' ) ) : '&lt;img alt="' . get_the_title() . '" src="' . get_bloginfo( 'stylesheet_directory' ) . '/images/related.png" /&gt;';

                    $related .= '&lt;/pre&gt;
&lt;ul&gt;
	&lt;li&gt;&lt;a title="Permanent Link to' . get_the_title() . '" href="' . get_permalink() . '" rel="bookmark"&gt;' . $img . get_the_title() . '&lt;/a&gt;&lt;/li&gt;
&lt;/ul&gt;
&lt;pre&gt;

';
                }
            }
        }

        if ( $related ) {

            printf( '&lt;/pre&gt;
&lt;div class="related-posts"&gt;
&lt;h3 class="related-title"&gt;Could You Like?&lt;/h3&gt;
&lt;ul class="related-list"&gt;%s&lt;/ul&gt;
&lt;/div&gt;
&lt;pre&gt;
', $related );

        }

        wp_reset_query();

    }
}
