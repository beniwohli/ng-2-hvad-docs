=========
models.py
=========

multilingual-ng and django-hvad share the same database schema. This means that
converting a project from ng to hvad should generally be possible without
schema migrations. A ng model can be converted into a hvad model like this:

multilingual-ng
---------------

.. code-block:: python

    class MyModel(models.Model):
        is_published = models.BooleanField()

        class Translation(multilingual.Translation):
            name = models.CharField(max_length=100)
            slug = models.SlugField(max_length=100)

        def __unicode__(self):
            return self.name

hvad
----

.. code-block:: python

    from nani.models import TranslatableModel, TranslatedFields

    class MyModel(TranslatableModel):
        is_published = models.BooleanField()

        translations = TranslatedFields(
            name = models.CharField(max_length=100),
            slug = models.SlugField(max_length=100),
        )

        def __unicode__(self):
            return self.safe_translation_getter('name', unicode(self.pk))

Notes
=====

 * a field is only accessible if it is translated in the current language
   (inefficient fallback methods notwithstanding). You should never try to access
   a translated field directly in :meth:`unicode`. Instead, use
   :meth:`self.safe_translation_getter`.
 * Don't try to mix hvad models with django-polymorphic, GeoDjango, django-mptt
   or generally model subclasses that depend on their own 
   :class:`~django.db.models.Manager` and :class:`~django.db.models.query.QuerySet`
   implementations. You will fail. Hard. Really. Don't do it. Honestly.
