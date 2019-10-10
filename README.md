# Resize-Image-en-Wordpress-Php

```php

function removeUrl($txt){
		return str_replace("http://reidco.lc", "", $txt);
	}

	function resize($filePath,$maxWidth,$maxHeight){
		if (!file_exists($filePath)) {
			return;
		}

		$editor = wp_get_image_editor( $filePath, array() );
		
		if (is_wp_error($editor)) {
			return;
		}

		// Get the dimensions for the size of the current image.
		$dimensions = $editor->get_size();

		$widthUp = false;

		if ($dimensions['width'] > $maxWidth || $dimensions['height'] > $maxHeight) {
			
			$fileName = end(explode("/", $filePath));

			$height = null;
			$cut = false;

			if ($dimensions['height'] > $maxHeight) {
				$height = $maxHeight;
				$cut = true;
			}

			$result = $editor->resize($maxWidth, $height, $cut);

			if (!is_wp_error($result)) {
				$editor->save($filePath);
			}
		}
	}

	/**
	 * 
	 * Esta función es actualiza las dimensiones de una imagen
	 * 
	 * @param int $post_id
	 * @return
	 */
	function resizeImagesOfPost($post_id){

		$root = $_SERVER['DOCUMENT_ROOT'];

		// Resize portada si aplica
		$img = get_field('portada',$post_id);
		if ($img) {
			resize($root.removeUrl($img),1920,980);
		}

		// Resize Galeria
		$img = get_field('galeria',$post_id);
		if ($img) {
			foreach ($img as $key) {
				resize($root.removeUrl($key),1920,980);
			}
		}
	}

  // Se ejecuta cada vez que se crea o actualiza un post cualquiera
	function runAfterPostSave($meta_id, $post_id, $meta_key='', $meta_value='') {
		resizeImagesOfPost($post_id);
	}

	add_action( 'updated_post_meta', 'runAfterPostSave', 10, 4 );
  
  ```
  
  Fuentes:
  
  https://developer.wordpress.org/reference/functions/wp_get_image_editor/
  https://developer.wordpress.org/reference/classes/wp_image_editor/
